# Configuration

hyprmcsr uses two types of configuration files, that are created, when running `hyprmcsr install` for the first time:

## Global config: `config.json`
- Located in `~/.config/hyprmcsr/config.json`
- Contains global settings like JAR download sources, pipewire loopback, etc.

## Profile config: `<profile>.profile.json`
- Located in `~/.config/hyprmcsr/<profile>.profile.json` (e.g. `default.profile.json`)
- Contains all profile-specific settings (Minecraft instance, binds, modes, etc.)

See [example.config.json](../example.config.json) and [example.default.profile.json](../example.default.profile.json) for full examples.

---

## Key fields

- **onStart**: Array of shell commands/scripts to run in the background when starting (e.g. starting helper tools, OBS, input-remapper, etc.).
- **onDestroy**: Array of shell commands/scripts to run in the background when stopping (e.g. cleanup, notifications, killing helper tools, stopping input-remapper).
- **onToggleBinds**: Array of shell commands/scripts to run whenever binds are toggled (e.g. notifications, custom actions).  
  The environment variable `$BINDS_ENABLED` is set to `1` (enabled) or `0` (disabled).
- **binds.toggleBinds**: Key combination to toggle binds.
- **binds.modeSwitch**: Key combinations for switching between window modes.
- **binds.custom**:  
  Define your own keybinds and associated commands here.  
  The commands will be executed with the environment variables `$WINDOW_ADDRESS`, `$SCRIPT_DIR`, `$PROFILE`, `$HYPRMCSR_PROFILE`, `$PRISM_INSTANCE_ID`, and `$MINECRAFT_ROOT` set.
- **modeSwitch.default**: Default window size, sensitivity, and optional `onEnter`/`onExit` arrays for commands to run when entering or exiting a mode.
- **modeSwitch.modes**: Per-mode overrides for size, sensitivity, and `onEnter`/`onExit` commands.
- **minecraft.prismPrefixOverride**: (Optional) Path to your PrismLauncher data directory.
- **minecraft.prismInstanceId**: Name or UUID of your PrismLauncher instance.
- **minecraft.windowClassRegex**:  
  Regular expression to detect the Minecraft window by its window class.
  This regex is used to identify the Minecraft window among all open windows.  
  You may need to adjust this regex to match the actual window class, e.g. if you are using a wrapper command, that changes the class (you can check the class of your windows with `hyprctl clients -j`).
- **minecraft.observeLog.enabled**: Enable or disable log observation for Minecraft state.
- **minecraft.onStart**: Array of shell commands/scripts to run after Minecraft has started (executed by `instance_wrapper.sh`).
- **pipewireLoopback.enabled**: Enable or disable Pipewire audio loopback/splitting.
- **pipewireLoopback.playbackTarget**: Audio output for Pipewire split (e.g., your headset).  
  Tip: You can leave this field empty. If loopback is enabled, running `install.sh` will automatically detect and set your default output here.
- **download.jar**: Array of GitHub repositories (or URLs in the future) for required JARs to be downloaded automatically.
- **autoDestroyOnExit**: If true, runs cleanup automatically when the main script exits.
- **requireSudo**: If true, you will be prompted for sudo at start and it will be kept alive for all commands in `onStart`/`onDestroy` (useful for input-remapper or other tools needing root).
- **minecraft.autoStart**:  
  If `true` (default), Minecraft will be started automatically by `hyprmcsr.sh`.  
  If `false`, you must start Minecraft yourself via PrismLauncher (GUI or CLI).  
  In both cases, all post-launch actions (window handling, audio, etc.) are handled by `instance_wrapper.sh` after Minecraft starts.
- **minecraft.prismWrapperCommand**:  
  Controls whether the instance wrapper is set automatically and which inner wrapper (like obs-gamecapture) is used.  
  Example:
  ```json
  "minecraft": {
    ...
    "prismWrapperCommand": {
      "autoReplace": true,
      "innerCommand": "obs-gamecapture"
    }
  }
  ```
  - If `autoReplace` is omitted or set to `true`, the wrapper will be set automatically and `instance_wrapper.sh` will be configured as the WrapperCommand for your PrismLauncher instance.
  - If `autoReplace` is `false`, **you must manually ensure that `instance_wrapper.sh` is set as the WrapperCommand in your PrismLauncher instance and that the environment variable `HYPRMCSR_PROFILE` is set correctly when launching Minecraft.**
  - `innerCommand` can be any wrapper tool (e.g., `"obs-gamecapture"`).
  - If you don't need a wrapper, you can omit this field.
- **minecraft.minecraftRootFolderOverride**: (Optional)  
  Set this to the absolute **or relative** path of your `.minecraft` folder if you want to override the default detection.  
  If you use a **relative path**, it will be resolved relative to the detected PrismLauncher instance folder.  
  Example:  
  ```json
  "minecraft": {
    "minecraftRootFolderOverride": ".minecraft"
  }
  ```
  If not set, the scripts will use the PrismLauncher instance config or the default path.

---

**Tip:**  
You can use variables like `$SCRIPT_DIR`, `$PROFILE`, `$HYPRMCSR_PROFILE`, `$PREVIOUS_MODE`, `$NEXT_MODE`, `$WINDOW_ADDRESS`, `$PRISM_INSTANCE_ID`, `$MINECRAFT_ROOT`, and `$BINDS_ENABLED` in your shell commands in `onStart`, `onDestroy`, `onEnter`, `onExit`, `onToggleBinds`, `minecraft.onStart`, and custom binds.
