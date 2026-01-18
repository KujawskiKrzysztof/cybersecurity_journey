# Google Dorking Commands Cheat Sheet

## **Basic Search Operators**

### **site:** - Search within a specific website or domain
- `site:example.com` - Search only example.com
- `site:.edu` - Search only educational sites
- `site:.gov` - Search only government sites

### **filetype:** or **ext:** - Search for specific file types
- `filetype:pdf` - Find PDF files
- `filetype:xls` - Find Excel files
- `filetype:doc` - Find Word documents
- `filetype:sql` - Find SQL files

### **inurl:** - Search for terms in the URL
- `inurl:admin` - URLs containing "admin"
- `inurl:login` - URLs containing "login"

### **allinurl:** - All terms must be in the URL
- `allinurl:admin login` - Both words in URL

### **intitle:** - Search for terms in page title
- `intitle:"index of"` - Pages with "index of" in title
- `intitle:admin` - Pages with "admin" in title

### **allintitle:** - All terms must be in title
- `allintitle:admin panel login`

### **intext:** - Search for terms in page body
- `intext:"password"` - Pages containing "password"

### **allintext:** - All terms must be in body text
- `allintext:username password email`

### **cache:** - View Google's cached version
- `cache:example.com` - Show cached page

### **link:** - Find pages linking to a URL
- `link:example.com`

### **related:** - Find similar websites
- `related:example.com`

### **info:** - Get information about a page
- `info:example.com`

---

## **Advanced Combinations**

### **Find exposed directories:**
```
intitle:"index of" site:example.com
intitle:"index of" "parent directory"
intitle:"index of" backup
```

### **Find login pages:**
```
inurl:admin intitle:login
inurl:login site:example.com
inurl:wp-admin
```

### **Find configuration files:**
```
filetype:env "DB_PASSWORD"
filetype:config intext:password
ext:xml inurl:web.config
```

### **Find database files:**
```
filetype:sql "INSERT INTO" "VALUES"
ext:sql intext:password
filetype:mdb inurl:admin
```

### **Find log files:**
```
filetype:log inurl:access.log
ext:log "username" "password"
```

### **Find backup files:**
```
filetype:bak inurl:backup
ext:old intext:password
site:example.com ext:bak
```

### **Find vulnerable servers:**
```
inurl:phpinfo.php
intitle:"Apache Status" "Server Version"
intitle:"Test Page for Apache Installation"
```

### **Find email addresses:**
```
site:example.com intext:"@example.com"
site:example.com filetype:xls intext:email
```

### **Find documents with sensitive info:**
```
filetype:pdf "confidential"
filetype:xls inurl:password
filetype:doc site:example.com confidential
```

---

## **Search Modifiers**

### **Quotation marks ("")** - Exact phrase matching
- `"exact phrase here"`

### **OR** - Either term (use CAPS)
- `site:example.com (login OR admin)`

### **AND** - Both terms (implicit, or use explicitly)
- `password AND username`

### **Minus (-)** - Exclude terms
- `site:example.com -www` - Exclude www subdomain
- `password -tutorial` - Exclude "tutorial"

### **Asterisk (*)** - Wildcard
- `"admin * panel"` - Matches any word between

### **Two periods (..)** - Number range
- `camera $100..$500` - Price range

### **Parentheses ()** - Group terms
- `site:example.com (inurl:admin OR inurl:login)`

---

## **Security Research Examples**

### **Find exposed cameras:**
```
inurl:view/index.shtml
intitle:"Live View / - AXIS"
inurl:ViewerFrame?Mode=
```

### **Find exposed databases:**
```
intitle:"phpMyAdmin" "Welcome to phpMyAdmin"
inurl:":3306" -git -stackoverflow
```

### **Find vulnerable web apps:**
```
inurl:wp-content/plugins/ vulnerability
powered by vBulletin site:example.com
```

### **Find exposed Git repositories:**
```
inurl:.git intitle:index.of
filetype:git
```

### **Find API keys:**
```
filetype:env "API_KEY"
ext:json api_key
intext:api_key filetype:log
```

---

## **Practical Tips**

1. **Combine operators** for more precise results:
   - `site:example.com filetype:pdf confidential`

2. **Use quotes** for exact phrases:
   - `"powered by WordPress 4.7"`

3. **Check specific subdomains**:
   - `site:admin.example.com`

4. **Search date ranges** (in Google Advanced Search):
   - Use the tools menu for custom date ranges

5. **Be ethical**: Use these techniques only for:
   - Security research on systems you own
   - Bug bounty programs
   - Authorized penetration testing
   - Educational purposes

---

## **Important Ethical & Legal Notes**

⚠️ **Use responsibly**: Google Dorking should only be used for legitimate purposes like security research on your own systems, authorized penetration testing, or bug bounty programs. Unauthorized access to systems is illegal in most jurisdictions.

These commands are powerful tools for security researchers, but they can also be misused. Always ensure you have proper authorization before testing any systems you don't own.