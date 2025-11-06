# Email Notification System

This document explains how to configure and use the email notification system for Advanced Sports Kenya.

## 📧 Email Features

The system automatically sends email notifications for:

### 1. **Booking Confirmation** 
- ✅ Sent immediately when a booking is created
- Includes booking details, confirmation ID, and instructions
- Beautiful HTML template with company branding

### 2. **Status Updates**
- 🔄 Sent when booking status changes (confirmed, cancelled, completed)
- Different styling based on status type
- Contextual messages and next steps

### 3. **Booking Reminders**
- ⏰ Can be sent 24 hours before the session
- Includes pre-session checklist and preparation tips
- Automated through cron jobs or manual API calls

### 4. **Cancellation Notifications**
- ❌ Special template for booking cancellations
- Includes cancelled booking details and rebooking options

## 🔧 Email Configuration

### Environment Variables

Update your `.env` file with proper email settings:

```properties
# Email Configuration
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your-company-email@gmail.com
EMAIL_PASS=your-app-password
```

### Gmail Setup (Recommended)

1. **Enable 2-Factor Authentication** on your Gmail account
2. **Generate App Password**:
   - Go to [Google Account Settings](https://myaccount.google.com/apppasswords)
   - Select "Mail" and "Other (Custom name)"
   - Enter "Advanced Sports Kenya" as the name
   - Copy the generated 16-character password
3. **Update Environment Variables**:
   ```properties
   EMAIL_HOST=smtp.gmail.com
   EMAIL_PORT=587
   EMAIL_USER=youremail@gmail.com
   EMAIL_PASS=xxxx xxxx xxxx xxxx  # Use the app password
   ```

### Other Email Providers

**Outlook/Hotmail:**
```properties
EMAIL_HOST=smtp-mail.outlook.com
EMAIL_PORT=587
EMAIL_USER=your-email@outlook.com
EMAIL_PASS=your-password
```

**Custom SMTP:**
```properties
EMAIL_HOST=mail.yourdomain.com
EMAIL_PORT=587  # or 465 for SSL
EMAIL_USER=noreply@yourdomain.com
EMAIL_PASS=your-password
```

## 🚀 API Endpoints

### Automatic Email Sending

Emails are automatically sent when:
- `POST /api/bookings` - Creates booking → Sends confirmation
- `PATCH /api/bookings/:id/status` - Updates status → Sends status update

### Manual Email Operations

```bash
# Send booking reminder
POST /api/bookings/:id/send-reminder

# Resend confirmation email
POST /api/bookings/:id/resend-confirmation

# Get bookings that need reminders
GET /api/bookings/reminders/due
```

### Example Usage

```javascript
// Send reminder email for specific booking
fetch('/api/bookings/64a7b8c9d1234567890abcde/send-reminder', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' }
});

// Get all bookings needing reminders
fetch('/api/bookings/reminders/due')
  .then(res => res.json())
  .then(data => console.log('Bookings needing reminders:', data.data));
```

## 🤖 Automated Reminders

### Cron Job Setup

Set up a daily cron job to send reminder emails:

```bash
# Run daily at 9 AM to send reminders for next day's bookings
0 9 * * * cd /path/to/your/backend && npm run email:reminders
```

### Manual Reminder Run

```bash
# Send reminder emails for tomorrow's bookings
npm run email:reminders
```

The script will:
1. Find all confirmed bookings for tomorrow
2. Send reminder emails to customers with email addresses
3. Log success/failure for each email
4. Provide summary report

## 📊 Email Templates

### Template Types

1. **Confirmation Email** (Green theme)
   - Booking details and confirmation ID
   - What to bring and arrive time
   - Contact information

2. **Status Update Email** (Color-coded by status)
   - Status change notification
   - Next steps based on new status
   - Booking details reminder

3. **Reminder Email** (Blue theme)
   - "Session tomorrow" notification
   - Pre-session checklist
   - Last-chance cancellation info

4. **Cancellation Email** (Red theme)
   - Cancellation confirmation
   - Rebooking encouragement
   - Contact details for questions

### Customizing Templates

Email templates are in `backend/utils/emailService.js`. You can customize:
- **Colors and branding** in the CSS styles
- **Content and messaging** in the HTML content
- **Company information** (phone, email, address)
- **Logo and images** by hosting them and updating URLs

## 🔍 Troubleshooting

### Common Issues

1. **Emails not sending**
   - Check environment variables are set
   - Verify email credentials
   - Check if 2FA is enabled (use app passwords)
   - Review server logs for error messages

2. **Emails going to spam**
   - Use a professional email address
   - Set up SPF/DKIM records for your domain
   - Include unsubscribe links
   - Avoid spam trigger words

3. **Gmail authentication errors**
   - Enable "Less secure app access" OR use App Passwords
   - Check if account is locked due to suspicious activity
   - Verify email and password are correct

### Debug Email Sending

Enable detailed logging:

```javascript
// In emailService.js, add debugging
console.log('Sending email to:', email);
console.log('Email config:', {
  host: process.env.EMAIL_HOST,
  port: process.env.EMAIL_PORT,
  user: process.env.EMAIL_USER
});
```

### Test Email Configuration

```bash
# Test sending a confirmation email
curl -X POST http://localhost:5000/api/bookings/YOUR_BOOKING_ID/resend-confirmation
```

## 📈 Monitoring

Monitor email delivery through:
- Server logs (`console.log` outputs)
- Email provider dashboard
- Customer feedback
- Failed email error tracking

### Success Metrics

Track these metrics:
- Email delivery rate
- Open rates (if using email tracking)
- Customer satisfaction with notifications
- Reduction in "where's my confirmation?" support tickets

## 🔐 Security Considerations

1. **Use App Passwords** instead of account passwords
2. **Store credentials** in environment variables, not code
3. **Use HTTPS** for all email-related API calls
4. **Validate email addresses** before sending
5. **Rate limit** email sending to prevent abuse
6. **Include unsubscribe** options for marketing emails

## 🎯 Best Practices

1. **Responsive Design**: Templates work on mobile and desktop
2. **Clear CTAs**: Include clear next steps and contact info
3. **Branding Consistency**: Use company colors and logo
4. **Timing**: Send confirmations immediately, reminders 24h prior
5. **Fallback**: Don't fail bookings if email fails
6. **Testing**: Test with real email addresses before deployment

## 📞 Support

For email configuration help:
- Check server logs first
- Verify environment variables
- Test with a simple email provider (Gmail)
- Contact your IT team for custom SMTP setup