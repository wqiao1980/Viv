MODE SHAPES - UPDATED WORKBOOK

RUN BUTTON
Open modeshapes_smart.xlsm in desktop Excel.
On Controls, edit B3 (default IL count), B4 (default CF count), B5 (node set).
Click Run extraction. Select one or more .dat files using Ctrl/Shift.
Enter IL and CF counts in the prompts. Defaults come from Controls.
Every selected file uses those counts and receives a separate results tab.
Review the completion report and save the workbook.
Counts: whole numbers 0-100 each, with at least one direction requested.

RESULTS
Tabs use the file name including .dat, truncated to Excel's 31-character limit.
Invalid tab-name characters are replaced. Collisions receive (2), (3), etc.
Reruns create new tabs and retain earlier results, even for the same file.
Failed files report errors and do not stop the remaining batch.
Parsing/validation failures leave previous results intact and create no tab.

Default 3 IL / 3 CF layout:
A: node number. B:D: IL U2. E:G: CF U3.
J2:L2: IL frequencies. J3:L3: CF frequencies (cycles/time).
J6:L7: source eigenvalue mode numbers. P:U: complete eigenvalue table.
Row 2 is an ADDED boundary: minimum printed node number minus 1, all zeros.
The zero boundary is included in both plots. It is not an Abaqus source result.
Original nodes follow, sorted by number, with signs and values intact.
For test.dat: node 1 with zeros, then original nodes 2 through 200.
Larger requests shift the frequency and eigenvalue tables right to avoid overlap.
IL starts at B; CF follows IL. Frequency starts at max(10, IL+CF+4).
Eigenvalue starts at max(16, frequency column + max(IL,CF) + 3).

LOCAL COORDINATES
IL: signed printed MAXIMUM U2 = 1, within tolerance 0.0005.
CF: signed printed MAXIMUM U3 = 1, within tolerance 0.0005.
For nodes with *TRANSFORM, request local nodal print output in Abaqus:
  *NODE PRINT, NSET=PL_SPAN, GLOBAL=NO
  U
GLOBAL=NO is the NODE PRINT default for transformed nodes; specify it explicitly.
Untransformed nodes use global directions. Define IL/CF directions consistently.
The macro copies components AS PRINTED; it does not calculate coordinate rotations.
If a transformed model's .dat contains global output, regenerate local output.
Displacements alone cannot identify or reconstruct nodal coordinate transformations.
*SYSTEM (node position input) is different from *TRANSFORM (nodal directions).
Reference: https://abaqus.uclouvain.be/English/SIMACAEKEYRefMap/simakey-r-nodeprint.htm

VBA COMMANDS (Alt+F11, then Ctrl+G for the Immediate window)
One file, 3 IL and 3 CF:
  ? ImportModeShapesSafe("C:\python_aba\viv\test.dat", 3, 3)
One file with custom counts and node set:
  ? ImportModeShapesSafe("C:\python_aba\viv\test.dat", 4, 2, "PL_SPAN")
All .dat files directly in a folder, excluding subfolders:
  ? ImportFolder("C:\python_aba\viv", 3, 3, "PL_SPAN")
Safe functions return OK/ERROR text without opening VBA error dialogs.
The lower-level ImportModeShapes subroutine raises errors for VBA callers to catch.

POWERSHELL COMMAND LINE
Keep Run-ModeShapes.ps1 beside modeshapes_smart.xlsm. Close the workbook first.
From that folder, run:
  .\Run-ModeShapes.ps1 -DatFolder "C:\python_aba\viv" -IL 3 -CF 3
Custom counts/node set:
  .\Run-ModeShapes.ps1 -DatFolder "C:\python_aba\viv" -IL 4 -CF 2 -NodeSet "PL_SPAN"
Optional: -Workbook "C:\path\modeshapes_smart.xlsm" selects another copy.
The script uses installed desktop Excel and saves successful result tabs.
It prints per-file status and reports failure if any file cannot be imported.
Normal macro/script policies apply. The script does not change security settings.

INPUT REQUIREMENTS
One Abaqus frequency step per file; U1/U2/U3/UR1/UR2/UR3 tables with MAXIMUM.
Default node set: AUTO. Each file's eigenmode output set is detected independently.
For example, test.dat uses PL_SPAN and test2.dat uses PIPE.
If several output sets exist, specify one in Controls B5 or the command argument.
An unavailable explicit set reports the available names instead of silently skipping.
Optional footnote fields such as RT are skipped before the six numeric components.
RT means rectangular transformation, as identified by the file's footnote.
Unique node numbers and identical node sets are required across selected modes.
The macro stops scanning shapes once both requested counts have been found.
Modes satisfying both direction criteria are rejected as ambiguous.
Frequency is cycles/time; it is Hz only if model time is seconds.
ModeShapes.bas is provided for reviewing/importing VBA separately.

