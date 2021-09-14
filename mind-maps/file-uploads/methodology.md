# Search upload page

## 1. Find languages and frameworks
- Browser extensions
  - [Wappalyzer](https://www.wappalyzer.com/apps)
- Online tools
  - [Wappalyzer lookup](https://www.wappalyzer.com/lookup/)
- Manually
  - [Burp](https://portswigger.net/burp)
  - [ZAP](https://www.zaproxy.org/)


## 2. Find Headers (server / x-powered-by)
- CLI tools
  - `curl -s -I https://target.com`
  - `wget -q -S https://target.com`
- Other banner grabbing


## 3. Find client-side filters
- Look at the source code
- How is our file accessed ?
  - Can we access it directly in an uploads folder?
  - Is it embedded in a page somewhere?
  - What's the naming scheme of the website?
- CLI tools
  - `gobuster dir -u https://target.com -w directory-list-2.3-big.txt -x php,txt,html,js`
- Determine the baseline "accepted" file
  - Attempt an innocent file upload
  - Attempt a malicious file upload

## 4. bypassing any client-side filters
- Turn off Javascript in your browser
- Intercept and modify the incoming page
- Intercept and modify the file upload
- Send the file directly to the upload point
  - `curl -X POST -F "parameter:value" -F "parameter:@file" https://target.com`

## 5. Find server-side filters
- Attempt a malicious file upload
  - If successfully upload a file with invalid file extension `e.g., testimage.invalidfileextension`, then:
    - the server probably uses an extension blacklist to filter uploaded files.  
  - If this upload fails, then:
    - maybe any file extension is passed through a whitelist.
- Try re-upload the accepted innocent file, but change the magic number to something expected to be filtered.
  - If the upload fails, then:
    - you know that the server is using a magic number-based filter.
- Try to upload the innocent file, intercept the request with Burp and change the MIME type of the upload to something expected to be filtered. 
  - If the upload fails, then:
    - you know that the server is filtering based on MIME types.
- Enumerate possible file length filters:
    - Try uploading a small file, then uploading progressively bigger files until you hit the acceptable file length limit.
    - If you're fortunate, then the error message of the original upload may outright tell you what the size limit is. 
    - A small file length limit may prevent you from uploading the reverse shells.
