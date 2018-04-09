# Overwiew

Expo is an simple and straightforward way to get a visual impression of all your current virtual desktops that many compositing window managers use.  It's not a very powerful approach, but a very intuitive one and especially fits workflows that use lots of temporary windows or those in which the workspaces are mentally arranged in a grid.

i3expo emulates that function within the limitations of a non-compositing window manager. By listening to the IPC, it takes a screenshot whenever a window or workspace event occurrs. Thanks to an extremely fast C library, this produces negligible overhead in normal operation and allows the script to remember what state you left a workspace in.

The script is run as a background process and reacts to signals in order to open its UI in which you get an overview of the known state of your workspaces and can select another with the mouse or keyboard.

Example output:

![Sample](img/ui.png)

# Dependencies

- Python 3
- PyGame
- i3ipc

# Usage

Compile the `prntscn.cc` as follows:

`gcc -shared -O3 -Wall -fPIC -Wl,-soname,prtscn -o prtscn.so prtscn.c -lX11`

The library might nor compile for you, in which case you have to remove the `extern "C" ` from the function definitions and also maybe change `Xutil.h` to `Xlib.h`. If a C programmer can tell me why the former may be necessary or submits a pull request, I'd be glad.

Put the prntscn.so in the same directory as the Python script (or adjust the location in the code).

Put `config` into `$XDG_CONFIG_DIR/i3expo/config` and at the very least adjust the grid settings and the number of workspaces. These are the only mandatory options. For the other options, `None` or invalid values will usually (when `ConfigParser` throws a `ValueError`) be interpreted as "use the default". Colors can be specified by using their PyGame names or in #fff or #ffffff hex.

Run `i3expod.py`, preferably in a terminal in order to catch any errors in this pre-alpha state.

Send `SIGUSR1` to `i3expod.py` to show the Expo UI, for example by adding a `bindsym` for `killall -s SIGUSR1 i3expod.py` to your i3 `config`. Send `SIGHUP` to have the application reload its configuration.

Navigate the UI with the mouse or with they keyboard using `hjkl`, the arrow keys, Return and Escape.

# Limitations

Since it works by taking screenshots, the application cannot know workspaces it hasn't seen yet. Furthermore, the updates are less continuous than you might be used to if you're coming from a compositing WM, particularly because i3 doesn't know about you changing tabs in a browser or a video playing for a long time.

Empty workspaces don't technically exist to i3 and are thus inaccessible in the default config because it's not possible to handle named inexistant workspaces. If you still want to access them, you will have to set `switch_to_empty_workspaces` to `True` and define your names under `Workspaces` like e. g. `workspace_1 = 1:Firefox`.

# Caution

This is pre-alpha software and pretty much untested. It works for my own, single monitor workflow with a 3x4 desktop grid and zero unusual use cases. There is not much input validation and no protection against you screwing up the layout or worse.

# Bugs

The UI currently cannot be displayed if there is a fullscreen window already present, even if it isn't fullscreened itself. It's not clear why this happens and if it's a general PyGame problem that can be fixed.

# Todo

It's theoretically feasible to take the window information from i3's tree and allow for dragging of windows from one workspace to another or even from container to container. However, this would be massively complex (especially on the UI side) and it's not clear if it would be worth the effort.

And getting it into a publishable state, obviously.

# Credit

Stackoverflow user JHolta for the screenshot library to be found in this thread: https://stackoverflow.com/questions/69645/take-a-screenshot-via-a-python-script-linux