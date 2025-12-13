# Script 12: Notification Setup - ntfy.sh Integration

**Status:** Ready to Execute ✅
**Phase:** Phase C (Optional)
**Date Created:** December 13, 2025

---

## Overview

Script 12 sets up real-time security notifications using ntfy.sh - a free, simple notification service that requires no email passwords or complex setup.

**Why ntfy.sh?**
- ✅ Completely FREE
- ✅ No account needed
- ✅ No email passwords exposed
- ✅ Works on phone, desktop, browser
- ✅ Instant notifications
- ✅ No infrastructure cost

---

## What This Script Does

### 1. Creates Alert Helper Script
- `/usr/local/bin/send-proxmox-alert` - Sends notifications to ntfy.sh

### 2. Configures SSH Login Notifications
- Notifies you every time someone logs in via SSH
- Shows: username, IP address, hostname
- Useful for detecting unauthorized access

### 3. Sets Up System Health Checks
- Hourly checks for high resource usage
- Alerts if CPU > 80%, Memory > 85%, Disk > 90%
- Runs automatically via crontab

### 4. Integrates with Fail2ban
- Sends notification when IP is banned
- Sends notification when IP is unbanned
- Shows which jail blocked the IP

### 5. Configures SMART Monitoring
- Alerts for disk health issues
- Sends high-priority notifications

### 6. Updates Management Alerts
- Notifies when security patches are automatically installed
- Shows number of packages updated

---

## How to Execute

### Option 1: Via SSH from Proxmox Host

```bash
# SSH into Proxmox
ssh -p 22022 -i ~/.ssh/ugreen_key root@192.168.40.60

# Run the script
bash /root/proxmox-hardening/12-notification-setup.sh
```

### Option 2: Via SSH from LXC 102 (Container)

```bash
# SSH into LXC 102
ssh -i ~/.ssh/ugreen_key sleszugreen@192.168.40.81

# Copy script to Proxmox and run
scp 12-notification-setup.sh root@192.168.40.60:/root/proxmox-hardening/
ssh root@192.168.40.60 'bash /root/proxmox-hardening/12-notification-setup.sh'
```

### Option 3: Direct Command

```bash
ssh -p 22022 -i ~/.ssh/ugreen_key root@192.168.40.60 \
  'bash /root/proxmox-hardening/12-notification-setup.sh'
```

---

## What You Need to Do

### Step 1: Run the Script ✅
```bash
bash /root/proxmox-hardening/12-notification-setup.sh
```

This will:
- Create all helper scripts
- Configure notifications
- Send a test notification
- Display instructions

**Duration:** ~2-3 minutes

### Step 2: Subscribe to Notifications
Choose ONE method:

**Method A: Web Browser (Instant, No App Needed)**
1. Go to: https://ntfy.sh/proxmox-ugreen-alerts
2. Keep page open in browser
3. Notifications appear instantly

**Method B: Mobile App (Recommended)**
1. Install ntfy app:
   - Android: https://play.google.com/store/apps/details?id=io.heckel.ntfy
   - iOS: https://apps.apple.com/app/ntfy/id1625396347

2. Open app and tap "+"

3. Enter topic: `proxmox-ugreen-alerts`

4. Save and done!

### Step 3: Test It
Try these to test notifications:

```bash
# SSH login test (should get notification)
ssh -p 22022 -i ~/.ssh/ugreen_key root@192.168.40.60

# Test alert script
/usr/local/bin/send-proxmox-alert "Test message" "low" "test"

# Check system health (may trigger alert if high usage)
/usr/local/bin/proxmox-health-check
```

---

## What Notifications You'll Receive

### 1. SSH Login Alerts 🔐
```
SSH Login: User root from IP 192.168.99.6 on ugreen
```
- Sent every time someone logs in
- Useful for detecting unauthorized access
- Low priority (but still visible)

### 2. Fail2ban Ban Alerts 🚫
```
IP 203.0.113.5 banned from sshd (attempt: 5/3)
```
- High priority alert
- Shows when IP is blocked
- Shows which attempt triggered the ban

### 3. System Health Alerts ⚠️
```
High CPU usage on ugreen: 85%
```
- Alerts when CPU > 80%, Memory > 85%, Disk > 90%
- Runs hourly automatically
- High priority if critical

### 4. Security Update Alerts 📦
```
Security updates installed: 15 packages on ugreen
```
- Notifies when automatic patches are applied
- Low priority

### 5. Disk Health Alerts 💾
```
SMART Alert on /dev/sda: Self-test failed
```
- High priority
- Critical for data safety

---

## Script File Location

After execution, the script will be available at:
```
/root/proxmox-hardening/12-notification-setup.sh
```

Files created by the script:
```
/usr/local/bin/send-proxmox-alert         - Main alert sender
/usr/local/bin/proxmox-health-check       - Health monitoring
/usr/local/bin/proxmox-upgrade-notify     - Update notifications
/usr/local/bin/smartd-alert               - SMART notifications
/etc/profile.d/ssh-alert.sh               - SSH login notifications
/etc/fail2ban/action.d/proxmox-ntfy.conf  - Fail2ban ntfy action
/tmp/NOTIFICATION-SETUP-INSTRUCTIONS.txt  - User instructions
```

---

## Customization

### Change Notification Topic
If you want a different (more private) topic name:

```bash
# Edit the topic in the alert script
sudo sed -i 's/proxmox-ugreen-alerts/my-private-topic/g' \
  /usr/local/bin/send-proxmox-alert

# Then subscribe with new topic in ntfy app
```

### Disable SSH Login Notifications
```bash
sudo rm /etc/profile.d/ssh-alert.sh
```

### Change Health Check Thresholds
```bash
sudo nano /usr/local/bin/proxmox-health-check

# Edit these lines:
# THRESHOLD_CPU=80
# THRESHOLD_MEMORY=85
# THRESHOLD_DISK=90
```

### Disable Health Checks
```bash
crontab -e
# Remove or comment the proxmox-health-check line
```

---

## Troubleshooting

### Not Receiving Notifications?

1. **Check internet connectivity:**
   ```bash
   ping ntfy.sh
   ```

2. **Test the alert script:**
   ```bash
   sudo /usr/local/bin/send-proxmox-alert "Test" "low" "test"
   ```

3. **Check logs:**
   ```bash
   tail -50 /root/proxmox-hardening/hardening.log
   ```

4. **Verify correct topic:**
   - Should be: `proxmox-ugreen-alerts`
   - Check: https://ntfy.sh/proxmox-ugreen-alerts

5. **Check ntfy app settings:**
   - Make sure notifications are not muted
   - Check phone notification settings

---

## Important Notes

⚠️ **Security Considerations:**
- Topic name is public - choose a unique name if privacy concerns
- Anyone with topic name can see messages
- Messages are kept for 24 hours on ntfy.sh
- NOT ENCRYPTED - don't use for sensitive data

✅ **Benefits:**
- Completely FREE
- No email passwords exposed
- No complex setup
- Works everywhere (phone, desktop, browser)
- Instant notifications
- No infrastructure cost

---

## After Execution

### You'll See:
```
✅ Notification Setup Complete

What was set up:
  ✅ Alert helper script: /usr/local/bin/send-proxmox-alert
  ✅ SSH login notifications
  ✅ System health checks (hourly)
  ✅ SMART failure alerts
  ✅ Fail2ban notifications

To subscribe to notifications:
  Option 1: https://ntfy.sh/proxmox-ugreen-alerts
  Option 2: Install ntfy app and subscribe to 'proxmox-ugreen-alerts'
```

### Next Steps:
1. Subscribe to notifications (see Step 2 above)
2. Test with SSH login
3. Check your phone/browser for test notification
4. Done! You're receiving security alerts now

---

## Optional: Script 13+

After Script 12, you can optionally run:
- **Script 13:** Additional Hardening
- **Script 14+:** More monitoring enhancements

But Script 12 is the last RECOMMENDED script. Everything else is optional.

---

## Questions?

If something doesn't work:
1. Check the log file: `/root/proxmox-hardening/hardening.log`
2. Review instructions: `/tmp/NOTIFICATION-SETUP-INSTRUCTIONS.txt`
3. Test connectivity: `ping ntfy.sh`
4. Check topic URL: https://ntfy.sh/proxmox-ugreen-alerts

---

**Status:** Ready to Execute ✅
**Difficulty:** Easy (fully automated)
**Required Knowledge:** None
**Time to Execute:** 2-3 minutes
**Time to Set Up Notifications:** 2 minutes (just subscribe)
