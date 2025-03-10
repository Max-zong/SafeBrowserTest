# SafeBrowserTest

## File Name Rules
### Format: xxx-yyy-(z).ext, 1-(z).ext
- xxx → Total file count (if zipped, includes the number of archived files, Or "1")
- yyy → Number of suspected malicious files, only can executed binary file or script file, for example, exe/bat/js/...
- z → Scan result indicating confirmed virus files
## Examples:
- 10-3-(2).zip → A zip file containing 10 files, 3 suspected and scanned, 2 confirmed as viruses
- 5-1-(0).exe → A zip file containing  5 files scanned, 1 suspected and scanned, 0 confirmed as a virus
- 1-(0).exe → An executable, 1 suspected and scanned, 0 confirmed as a virus
- 1-(1).exe → An executable, 1 suspected and scanned, 1 confirmed as a virus
## How to Test:
- Click target file in the current page
- In the traget file page, click "View raw", which will trigger target file's downloading.
- When download completed, we can see the results, if you want to check the inner logic, you can enable log
## Log inspect:
When you download the "4-2-(1).zip" firstly. 
1. The Send json Log:
2. The Avra're sponse:
3. The Telemetry response:

When you click the same file again.
1.The Send Json Log: None
2.The Avira're response:None
3.The  Telemetry response:

# SafeBrowser Download Protection Feature Test

## File Name Rules
### Format: xxx-yyy-(z).ext, 1-(z).ext
- xxx → Total file count (if zipped, includes the number of archived files, or "1" for a single file)
- yyy → Number of suspected malicious files (only executable binary or script files, e.g., .exe, .bat, .js, etc.)
- z → Scan result indicating the number of confirmed virus files
### Examples
- 10-3-(2).zip → A ZIP file containing 10 files, 3 suspected as malicious, and 2 confirmed as viruses
- 5-1-(0).exe → An EXE file containing 5 scanned files, 1 suspected as malicious, and 0 confirmed as a virus
- 1-(0).exe → A single executable file, 1 suspected as malicious, and 0 confirmed as a virus
- 1-(1).exe → A single executable file, 1 suspected as malicious, and 1 confirmed as a virus
## How to Test
⚠️ Important: Disable Real-Time Virus Protection
Before testing, disable system or third-party real-time virus protection, as it may automatically delete infected files when unzipping. This can prevent you from retrieving file hashes.
### Test Steps:
- Click the target file on the current page.
- On the target file’s page, click "View raw" to trigger the file download.
- Once the download is complete, review the results.
- If you want to inspect the inner logic, enable logging.
### Log Inspection:
#### Enable Logging
To capture logs, run with the following arguments:
```
--enable-logging=stderr --v=2
```
#### First-time Download (e.g., "4-2-(1).zip")
- Sent JSON Log: (Logs related to the file request)
```
[Avira APC] Avira APC query JSON: {"flags":0,"metadata":{"os_type":1,"os_vbuild":0,"os_vmajor":10,"os_vminor":0},"sha256":{"7ba07adbdb86eafd252678c037fd1a59c15400ca5c38426cb5dce4030f1c7b7d":{"gen_data":{"file_path":"inst (7).exe"},"size":4118496},"a6259300d4d7940b3f1f7cd8c482acdf9e239bddbb6366cf39c8e60d1b92b2aa":{"gen_data":{"file_path":"windows.bat"},"size":187}}}
```
- Avira's Response: (Antivirus scan results)
```
{"sha256": {"7ba07adbdb86eafd252678c037fd1a59c15400ca5c38426cb5dce4030f1c7b7d": {"cat": 47, "status": "OK", "det_name": "PUA/Redcap", "ttl": 3600, "known": false, "classification_type": "static"}, "a6259300d4d7940b3f1f7cd8c482acdf9e239bddbb6366cf39c8e60d1b92b2aa": {"cat": 0, "status": "OK", "det_name": "", "ttl": 7200, "known": false, "classification_type": "dynamic"}}}
```
- Telemetry: 
```
[Avira APC] Sent DownloadScanResultEvent:{
   "files_is_cache_hit": 0,
   "files_scanned_successfully": 2,
   "files_to_be_scanned": 2,
   "files_unsafe": 1
}
[Avira APC] Sent ReputationPerformanceEvent:{
   "error": "net::OK",
   "scan_preparation": 1088,
   "scan_response_code": 200,
   "scan_responsetime": 357
}
```
#### Re-downloading the Same File
- Sent JSON Log: None (No new request sent)
- Avira's Response: None (Cached result used)
- Telemetry: 
```
[Avira APC] Cache hit: reason = 21, result = 5
[Avira APC] Sent DownloadScanResultEvent:{
   "files_is_cache_hit": 1,
   "files_scanned_successfully": 1,
   "files_to_be_scanned": 1,
   "files_unsafe": 1
}
```