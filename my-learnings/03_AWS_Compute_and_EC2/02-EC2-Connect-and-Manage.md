# Connect and Manage EC2 Instance

## Topics Covered
- SSH connection to EC2 instance using `.pem` key
- Updating EC2 packages
- Installing Apache server
- Upload Static HTML Page
- Security Group Configuration for HTTP
- Test Static HTML Page in Browser
- Precautions After Completing Tasks
- Summary of Errors and Fixes

---

## 1. SSH Connection to EC2

- Login command: 

```bash
ssh -i <your-key>.pem ec2-user@<Public-IP>
```
- Make sure `.pem` key permissions are correct:

```bash
chmod 400 <your-key>.pem
```

- Connect to your EC2 instance and verify you are logged in: 

```bash
[ec2-user@ip-xxx-xx-xx-xx ~]$
```

---

## 2. Update EC2 Server Packages

- Always update the server packages before installing new software:
```bash
sudo dnf update -y
```
- **Why:** ensures all software is updated, secure, and compatible

---

## 3. Install Apache Server

- Install Apache:

```bash
sudo dnf install httpd -y
```

- Start Apache and enable it to run on boot:

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

- Why: Apache turns the EC2 instance into a web server for hosting static HTML pages

---

## 4. Upload Static HTML Page

- Problem faced 1: Writing HTML with !

```bash
sudo echo "<h1>Hello Cloud, I'm Stark!</h1>" > /var/www/html/index.html
```

- Error: `!-event not found`
- Cause: `!` triggers Bash history expansion
- Fix: remove `!` or keep HTML simple
- **Problem faced 2: Permission denied**

```bash
sudo echo "<h1>Hello Cloud, Stark is learning</h1>" > /var/www/html/index.html
```

- Error: `Permission denied`
- Cause: `>` redirection runs as normal user, not root
- Fix: Use `tee` to write as root
- Correct command:

```bash
echo "<h1>Hello Cloud, Stark is learning</h1>" | sudo tee /var/www/html/index.html
```

- **Explanation:**

- `echo` → generates text
- `|` (pipe) → passes output to the next command
- `sudo tee` → writes content to the file as root and displays output

- Result: HTML file created successfully

---

## 5. Security Group Configuration for HTTP

- By default, EC2 allows only SSH (port 22)
- To access the HTML page from a browser:

1. Go to EC2 Console → Security Groups → Inbound Rules
2. Add a rule:

    - Type: HTTP
    - Protocol: TCP
    - Port Range: 80
    - Source: Anywhere (0.0.0.0/0)

- **Why:** without this, the browser cannot reach your web server

---

## 6. Test Static HTML Page in Browser

- Open browser and enter your EC2 public IP:

`http://<Public-IP>`

- Expected output:

`Hello Cloud, Stark is learning`

---

## 7. Precautions After Completing Tasks

- After uploading your application successfully:
    - **Exit SSH session:**

```bash
exit
```

- Stop your EC2 instance from AWS Console to save costs
- Why: stopping instance prevents running cost; exiting SSH avoids confusion in terminal
- You can reuse the same EC2 instance for multiple days; free-tier usage is safe

---

## 8. Summary of Errors and Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| `!-event not found` | `!` triggers Bash history expansion | Remove `!` or use simple text |
| `Permission denied` | `>` redirection runs as normal user | Use `sudo tee` instead |
| Cannot access page | Security group blocking HTTP | Open port 80 inbound rule |

---
