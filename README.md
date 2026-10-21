# Random Select

**Random Select** is a lightweight Windows command-line utility written
in PowerShell for randomly selecting files from the current directory.
You can list the selected files, prepare them for copying or moving
through the Windows clipboard, or package them into a dated ZIP archive.

A companion batch file (`rs.bat`) makes the PowerShell script easier to
launch from Command Prompt or a terminal.

## Features

-   Randomly select a specified number of files from the current
    directory.
-   List the selected files using their full paths.
-   Put selected files on the Windows clipboard for copying or cutting
    into another location.
-   Create a ZIP archive containing selected files, named with the
    current date.
-   Use `-all` to apply an operation to every file in the current
    directory.
-   Launch the utility through either PowerShell or the included batch
    wrapper.

## Requirements

-   Windows.
-   Windows PowerShell with support for `Get-ChildItem`, `Get-Random`,
    and `Compress-Archive` (PowerShell 5.1 or a compatible version is
    recommended).
-   For `-copy` and `-move`, access to the Windows desktop clipboard and
    the `System.Windows.Forms` assembly.
-   The script and batch file should be kept together.

> **Note:** The batch file runs `random_select.ps1`. Make sure the
> PowerShell script is named exactly `random_select.ps1` and is in the
> same folder as `rs.bat`. If your downloaded file is named
> `random_select(1).ps1`, rename it to `random_select.ps1` or update the
> batch file to use that filename.

## Installation

1.  Download or clone this repository.
2.  Place `random_select.ps1` and `rs.bat` in the same folder.
3.  Open Command Prompt or PowerShell in that folder.
4.  Run the command examples below.

No separate package installation is required for the core script.

## Usage

### Run from Command Prompt

Use the batch wrapper:

``` bat
rs.bat 5 -list
```

### Run from PowerShell

``` powershell
.\random_select.ps1 5 -list
```

### Syntax

``` text
random_select <count> [operation]
```

`<count>` is the number of files to select. It must be a positive
integer unless `-all` is used. If the requested count is greater than
the number of files available, the script selects all available files.

## Options

  -----------------------------------------------------------------------
  Option                  Alias                   Description
  ----------------------- ----------------------- -----------------------
  `-copy`                 `-c`                    Put the selected files
                                                  on the clipboard with
                                                  copy behavior.

  `-move`                 `-m`                    Put the selected files
                                                  on the clipboard with
                                                  cut/move behavior.

  `-list`                 `-l`                    Print the full paths of
                                                  the selected files.

  `-zip`                  `-z`                    Create a ZIP archive
                                                  containing the selected
                                                  files.

  `-all`                  `-a`                    Select all files in the
                                                  current directory
                                                  instead of selecting a
                                                  random subset.

  `-help`                 `-h`                    Display the help
                                                  message.
  -----------------------------------------------------------------------

The script also displays help when run without parameters.

## Examples

### 1. Show help

``` powershell
.\random_select.ps1 -help
```

### 2. List 5 randomly selected files

``` powershell
.\random_select.ps1 5 -list
```

The script prints the full paths of the selected files and exits.

### 3. Copy 3 randomly selected files

``` powershell
.\random_select.ps1 3 -copy
```

The selected files are placed on the Windows clipboard. Navigate to the
destination folder and press **Ctrl+V** to paste them.

### 4. Cut/move 2 randomly selected files

``` powershell
.\random_select.ps1 2 -move
```

The selected files are placed on the clipboard with move/cut behavior.
Navigate to the destination folder and press **Ctrl+V**. The files are
not moved immediately when the command runs; the move occurs when you
paste them into a destination.

### 5. Create a ZIP archive from 4 selected files

``` powershell
.\random_select.ps1 4 -zip
```

The script creates a ZIP file named with today's date, such as:

``` text
2026-10-21.zip
```

The archive is created in the current directory. If a ZIP file with that
date-based name already exists, the script reports that it already
exists and exits rather than overwriting it.

### 6. List every file

``` powershell
.\random_select.ps1 -all -list
```

### 7. Copy every file

``` powershell
.\random_select.ps1 -all -copy
```

### 8. Create a ZIP containing every file

``` powershell
.\random_select.ps1 -all -zip
```

### 9. Use short aliases

``` powershell
.\random_select.ps1 5 -l
.\random_select.ps1 3 -c
.\random_select.ps1 2 -m
.\random_select.ps1 4 -z
.\random_select.ps1 -a -l
```

## How it works

1.  The script reads files from the **current working directory**.
2.  It selects the requested number of files at random, or all files
    when `-all` is supplied.
3.  It reports how many files were selected.
4.  It performs the requested operation:
    -   `-list`: prints the selected file paths.
    -   `-zip`: compresses the selected files into a date-named ZIP
        archive.
    -   `-copy`: places the file list on the Windows clipboard with copy
        behavior.
    -   `-move`: places the file list on the Windows clipboard with
        cut/move behavior.

The selection is limited to files directly inside the current directory;
subdirectories are not recursively searched. The script also excludes a
file whose name matches the running script's command name.

## Important behavior and limitations

-   **Current directory matters:** The script operates on the directory
    you are currently in, not automatically on the directory where the
    script is stored.
-   **Files only:** It selects files in the current directory, not
    folders, and does not recurse into subdirectories.
-   **Count must be positive:** A count of zero or a negative count is
    rejected unless `-all` is used.
-   **Count larger than available files:** If the requested count
    exceeds the number of available files, all available files are
    selected.
-   **Operation selection:** Use one operation switch at a time
    (`-list`, `-zip`, `-copy`, or `-move`) for predictable results.
-   **ZIP name collisions:** The ZIP operation stops if a file with
    today's date as its name already exists.
-   **Clipboard operations require Windows:** `-copy` and `-move` use
    Windows Forms clipboard functionality and may not work in
    non-interactive sessions or environments without a Windows desktop.
-   **The clipboard operation is not an immediate file operation:** For
    `-copy` and `-move`, use **Ctrl+V** in the target folder to complete
    the action.
-   **Execution policy:** The batch wrapper invokes PowerShell with
    `-ExecutionPolicy Bypass` for that process. If you prefer not to use
    this wrapper, run the PowerShell script directly according to your
    organization's security policy.

## Troubleshooting

### `random_select.ps1` cannot be found

Make sure the script is named `random_select.ps1` and is in the same
directory as `rs.bat`. The batch file uses its own directory to locate
the script.

### "No files found in this directory."

Run the command from a directory containing files, or change to the
intended directory first:

``` powershell
Set-Location "C:\Path\To\Your\Files"
```

### "Invalid count specified."

Provide a positive integer:

``` powershell
.\random_select.ps1 5 -list
```

Or use `-all` to select all files:

``` powershell
.\random_select.ps1 -all -list
```

### Clipboard options fail

Run the command in a normal, interactive Windows desktop session.
Clipboard functionality depends on `System.Windows.Forms` and access to
the Windows clipboard.

### A ZIP file already exists

The script does not overwrite an existing date-named archive. Move or
rename the existing ZIP file, or run the command on another date.

## Project structure

``` text
.
├── random_select.ps1   # Main PowerShell script
├── rs.bat              # Batch wrapper for launching the script
└── README.md           # Project documentation
```

## Safety

The `-list` and `-zip` options do not intentionally delete the selected
source files. The `-copy` and `-move` options use the Windows clipboard;
with `-move`, files are moved when you paste them into a destination
that honors the clipboard's move behavior. Check the selected files and
destination before pasting.

## Contributing

Contributions and suggestions are welcome.

1.  Fork the repository.
2.  Create a branch for your change.
3.  Make and test your changes on Windows.
4.  Open a pull request describing the change and how it was tested.

When reporting a bug, include your Windows/PowerShell version, the
command you ran, and the error message. Avoid including sensitive file
paths or private data in public issue reports.

## License

I Use The MIT License Is permissive open-source license on GitHub. It allows anyone to freely use, copy, modify, merge, publish, distribute, sublicense, and sell your software, provided they include your original copyright notice and the license text

## Author

Mrsearcher1996 And AI
