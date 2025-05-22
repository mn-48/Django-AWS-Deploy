# Disk Space Monitoring Script

Here's a Python script that monitors disk space usage and sends an email notification if it exceeds 90%:

```python
#!/usr/bin/env python3

import shutil
import smtplib
import socket
from email.mime.text import MIMEText

# Configuration
THRESHOLD = 90  # Percentage threshold for alert
SMTP_SERVER = 'your.smtp.server.com'
SMTP_PORT = 587
EMAIL_FROM = 'monitor@yourdomain.com'
EMAIL_TO = 'admin@yourdomain.com'
EMAIL_USER = 'smtp_username'
EMAIL_PASS = 'smtp_password'

def check_disk_usage():
    """Check disk usage and return percentage used"""
    disk_usage = shutil.disk_usage('/')
    percent_used = (disk_usage.used / disk_usage.total) * 100
    return round(percent_used, 2)

def send_alert(usage):
    """Send email alert when disk usage exceeds threshold"""
    server_name = socket.gethostname()
    subject = f"Disk Space Alert on {server_name}"
    body = f"""
    Server: {server_name}
    Current disk usage: {usage}%
    """
    
    msg = MIMEText(body)
    msg['Subject'] = subject
    msg['From'] = EMAIL_FROM
    msg['To'] = EMAIL_TO
    
    try:
        with smtplib.SMTP(SMTP_SERVER, SMTP_PORT) as server:
            server.starttls()
            server.login(EMAIL_USER, EMAIL_PASS)
            server.send_message(msg)
        print("Alert email sent successfully.")
    except Exception as e:
        print(f"Failed to send email: {e}")

def main():
    usage = check_disk_usage()
    
    if usage > THRESHOLD:
        send_alert(usage)
    else:
        print("**Disk usage is below 90%.**")

if __name__ == "__main__":
    main()
```

## How to Use This Script

1. Save the script as `disk_monitor.py`
2. Make it executable: `chmod +x disk_monitor.py`
3. Edit the configuration section with your SMTP server details
4. Run the script: `./disk_monitor.py` or `python3 disk_monitor.py`

## Requirements

- Python 3
- Access to an SMTP server for sending emails

## Notes

- The script checks the root filesystem (`/`) by default. If you need to check a different partition, modify the `shutil.disk_usage()` parameter.
- You may need to adjust SMTP settings based on your email provider's requirements.
- For production use, consider:
  - Adding logging
  - Setting up as a cron job to run regularly
  - Using environment variables for sensitive credentials
  - Adding error handling for edge cases
