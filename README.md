### **Nuclei: Fast and Customizable Vulnerability Scanner**
Nuclei is an open-source vulnerability scanner that helps automate security testing using templates. It is widely used for penetration testing, bug bounty hunting, and security assessments.

---

### **Key Features of Nuclei**
- **Fast & Efficient**: Multi-threaded scanning for high-speed results.
- **Customizable Templates**: Uses YAML-based templates for detecting various vulnerabilities.
- **Integration-Friendly**: Can be used with other tools like Burp Suite, Metasploit, or automation scripts.
- **Supports Multiple Protocols**: HTTP, DNS, TCP, SSL, WebSocket, and more.
- **CI/CD Integration**: Can be added to security pipelines for continuous scanning.

---

### **Installation**
#### **On Linux/macOS:**
```bash
curl -s https://api.github.com/repos/projectdiscovery/nuclei/releases/latest | grep "browser_download_url.*nuclei-linux-amd64" | cut -d '"' -f 4 | wget -qi - && chmod +x nuclei-linux-amd64 && sudo mv nuclei-linux-amd64 /usr/local/bin/nuclei
```
#### **On Windows (Using PowerShell):**
```powershell
Invoke-WebRequest -Uri "https://github.com/projectdiscovery/nuclei/releases/latest/download/nuclei-windows-amd64.zip" -OutFile "nuclei.zip"; Expand-Archive nuclei.zip -DestinationPath .
```

---

### **Basic Usage**
1. **Update Nuclei Templates** (Important before scanning)
   ```bash
   nuclei -update-templates
   ```
2. **Scan a Target Website**
   ```bash
   nuclei -u https://example.com
   ```
3. **Scan Using a Specific Template**
   ```bash
   nuclei -t templates/cves/ -u https://example.com
   ```
4. **Scan a List of URLs**
   ```bash
   nuclei -l urls.txt
   ```
5. **Filter by Severity**
   ```bash
   nuclei -t cves/ -u https://example.com -severity critical
   ```
6. **Output Results to a File**
   ```bash
   nuclei -u https://example.com -o results.txt
   ```

---

### **Automating GitHub Scanning with Nuclei**
Since you want to automate **GitHub scanning with Nuclei**, you can:
1. **Fetch URLs from GitHub repositories**
2. **Pass them to Nuclei for automated security scans**
3. **Integrate with CI/CD for continuous monitoring**

### **Using Custom Templates in Nuclei**  
You can create and add **custom YAML templates** to Nuclei for detecting specific vulnerabilities.

---

## **1. Understanding Nuclei Templates**
Nuclei templates are written in **YAML format** and consist of:  
- **Info**: Metadata (name, severity, author, etc.).  
- **Requests**: HTTP requests & response conditions.  

---

## **2. Creating a Custom Template**
Here’s an example template to detect a vulnerable version of **Apache Tomcat**:

### **File: `custom-tomcat-detect.yaml`**
```yaml
id: apache-tomcat-detect

info:
  name: Apache Tomcat Version Detection
  author: Harsh
  severity: info
  tags: apache,tomcat

requests:
  - method: GET
    path:
      - "{{BaseURL}}/manager/html"

    matchers:
      - type: word
        words:
          - "Apache Tomcat"
```

---

## **3. Running Nuclei with Custom Template**
Once you've created the template, you can run:
```bash
nuclei -u https://example.com -t custom-tomcat-detect.yaml
```

Or, if you have multiple custom templates in a directory:
```bash
nuclei -u https://example.com -t custom-templates/
```

---

## **4. Automating GitHub URL Scanning with Custom Templates**
Here’s a script to:  
- Extract URLs from GitHub repos  
- Scan them using **custom Nuclei templates**  

### **Script: `github_nuclei_scan.sh`**
```bash
#!/bin/bash

# GitHub repo URL list
REPO_LIST="repos.txt"

# Extract URLs (Modify as needed)
cat $REPO_LIST | while read repo; do
    echo "Scanning repo: $repo"
    nuclei -u "$repo" -t custom-templates/
done
```
### **Run the script**
```bash
chmod +x github_nuclei_scan.sh
./github_nuclei_scan.sh
```

---

## **5. Adding Custom Templates to the Official Directory**
You can place your custom templates inside Nuclei's template directory:
```bash
mkdir -p ~/.nuclei-templates/custom
mv custom-tomcat-detect.yaml ~/.nuclei-templates/custom/
```
Run with:
```bash
nuclei -u https://example.com -t ~/.nuclei-templates/custom/
```

---
### **Multi-Scan with Nuclei (Custom Script)**
This script will:  
✅ Extract URLs from **GitHub repositories**  
✅ Run **multiple templates** for scanning  
✅ Save results in a structured format  

---

## **6. Multi-Scan Script**
### **File: `multi_nuclei_scan.sh`**
```bash
#!/bin/bash

# Check if URL file exists
URL_FILE="urls.txt"
TEMPLATE_DIR="custom-templates/"
OUTPUT_FILE="nuclei_scan_results.txt"

if [ ! -f "$URL_FILE" ]; then
    echo "Error: $URL_FILE not found!"
    exit 1
fi

# Create output file
echo "Nuclei Scan Results - $(date)" > $OUTPUT_FILE
echo "--------------------------------" >> $OUTPUT_FILE

# Start scanning
while read -r url; do
    echo "[+] Scanning: $url"
    
    # Run Nuclei with multiple templates
    nuclei -u "$url" -t "$TEMPLATE_DIR" -o temp_results.txt
    
    # Append results to the output file
    cat temp_results.txt >> $OUTPUT_FILE
    echo "--------------------------------" >> $OUTPUT_FILE

done < "$URL_FILE"

echo "Scan completed. Results saved in $OUTPUT_FILE."
```

---

## **7. How to Use It**
### **Step 1: Prepare Files**
- **`urls.txt`** → List of GitHub URLs to scan  
- **`custom-templates/`** → Folder with multiple Nuclei templates  

### **Step 2: Run the Script**
```bash
chmod +x multi_nuclei_scan.sh
./multi_nuclei_scan.sh
```

---

## **8. Expected Output (`nuclei_scan_results.txt`)**
```
Nuclei Scan Results - Sat Feb 23 2025
--------------------------------
[+] Scanning: https://github.com/example/repo1
High Severity Vulnerability Found: CVE-2023-XYZ
--------------------------------
[+] Scanning: https://github.com/example/repo2
No vulnerabilities detected.
--------------------------------
```

---
