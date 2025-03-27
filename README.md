# SafeBrowser Download Protection Feature Test

## File Name Rules
### Format: xxx-yyy-(z).ext, 1-(z).ext
- xxx → Total file count (if zipped, includes the number of archived files, or "1" for a single file)
- yyy → Number of suspected malicious files (only executable binary or script files, e.g., .exe, .bat, .js, etc.)
- z → Scan result indicating the number of confirmed virus files
### Examples
- 10-3-(2).zip → A ZIP file containing 10 files, 3 suspected as malicious, and 2 confirmed as viruses
- 5-1-(0).zip → A ZIP file containing 5 files, 1 suspected as malicious, and 0 confirmed as a virus
- 1-(0).exe → A single executable file, 1 suspected as malicious, and 0 confirmed as a virus
- 1-(1).exe → A single executable file, 1 suspected as malicious, and 1 confirmed as a virus
## How to build
⚠️ Important: https://github.com/redbrickmedia/shift-browser/wiki/How-to-set-download-protection-key
This feature requires the Aviral API key to be set before building.
## How to Test
⚠️ Important: Disable Real-Time Virus Protection
Before testing, disable system or third-party real-time virus protection, as it may automatically delete infected files when unzipping. This can prevent you from retrieving file hashes.

⚠️ Currently, encrypted ZIP files are not supported by download protection.

⚠️ Please use chrome://downloads to verify scanning results. There are known bugs affecting the drop-download UI's display of malicious statuses; a fix has been implemented but is still under review.
### Test Steps:
- Navigate to chrome://settings/security and enable Safe Browsing Standard Protection.
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
[Avira APC] Avira APC query JSON: {"flags":0,"metadata":{"os_type":1,"os_vbuild":0,"os_vmajor":10,"os_vminor":0},
"sha256":
{"7ba07adbdb86eafd252678c037fd1a59c15400ca5c38426cb5dce4030f1c7b7d":
    {"gen_data":{"file_path":"inst (7).exe"},"size":4118496},
"a6259300d4d7940b3f1f7cd8c482acdf9e239bddbb6366cf39c8e60d1b92b2aa":
    {"gen_data":{"file_path":"windows.bat"},"size":187}
}}
```
- Avira's Response: (Antivirus scan results)
```
{"sha256": 
{"7ba07adbdb86eafd252678c037fd1a59c15400ca5c38426cb5dce4030f1c7b7d":
     {"cat": 47, "status": "OK", "det_name": "PUA/Redcap", "ttl": 3600, "known": false, "classification_type": "static"}, 
"a6259300d4d7940b3f1f7cd8c482acdf9e239bddbb6366cf39c8e60d1b92b2aa": 
    {"cat": 0, "status": "OK", "det_name": "", "ttl": 7200, "known": false, "classification_type": "dynamic"}}}
```
**About Avira's response, the key "cat" represents the scan result, indicating whether the file is malicious or safe. For details on "cat" value definitions, please refer to the File Reputation API (APC).pdf document.**
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
