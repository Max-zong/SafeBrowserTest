# SafeBrowserTest

## File Name Rules
### Format: xxx-yyy-(z).ext, 1-(z).ext
- xxx → Total file count (if zipped, includes the number of archived files, Or "1")
- yyy → Number of suspected malicious files
- z → Scan result indicating confirmed virus files
## Examples:
10-3-(2).zip → A zip file containing 10 files, 3 suspected as malicious, 2 confirmed as viruses
5-1-(0).exe → An executable with 5 files scanned, 1 suspected, 0 confirmed as a virus
8-2.zip → A zip file with 8 files, 2 suspected as malicious (no confirmed virus files)
