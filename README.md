# How to get Reminders in Gmail, like Inbox had

I was an avid user of Reminders in Inbox. Since Inbox was deprecated, I've come up with a system that's almost as good.

Basically Reminders are emails. See example screenshots:

![web email](https://user-images.githubusercontent.com/10929390/63665525-8e616680-c780-11e9-948d-0fbfcdb6adae.png)

<p align="center">
  <img src="https://user-images.githubusercontent.com/10929390/63665526-8e616680-c780-11e9-9f9f-be9e7592b4cb.png" width="300">
</p>

## How to consume reminders

Reminders are just emails, so you can snooze/archive like normal.

If you want a minimal Inbox-like UI, use the [Simplify extension](https://chrome.google.com/webstore/detail/simplify-gmail/pbmlfaiicoikhdbjagjbglnbfcbcojpj?hl=en).
I recommend using this extension with Gmail's Soft Gray theme, [as JR recommends](https://www.computerworld.com/article/3387139/gmail-redesigned.html).

## How to create reminders

### Summary

| | | Extra work compared to Inbox |
| --- | --- | --- | 
| One-time reminder on desktop | Send email to self | 5 keystrokes |
| One-time reminder on Android | [Email Yourself widget](https://play.google.com/store/apps/details?id=com.dzogchenltd.emailyourself) | 1 tap |
| Repeating reminder | Repeating calendar event | None |

### Desktop

1. Press `c` to compose an email
1. Type the first letter of your email. Press `Enter` to select your email from the auto-complete drop-down. Press `tab`
1. Put reminder in email subject
1. If you want the Reminder to show up right away: Press `tab tab Enter`
1. If you want to schedule for later, use the `Scheduled Send`
feature. Click the arrow next to `Send button`.

*Extra work compared to Inbox*
- 3 keystrokes from Step 2)
- 2 keystrokes from Step 4) (`tab tab`)

#### Create an editable reminder

Occasionally I'll want an editable reminder. For example, I may use a reminder
to keep track of remaining work on a small project.

- Compose an email and enter reminder
- Find the email in the Drafts folder and move to Inbox
- Now you can edit reminder

### Android

Inbox had a widget to create a Reminder. Similarly, I use a widget to send email, with my address preconfigured as the recipient.

One-time setup:
1. One-time setup: Install [Email Yourself Android app](https://play.google.com/store/apps/details?id=com.dzogchenltd.emailyourself). I use this many times a day; it's well worth the $5 to me. Grant both requested permissions.
1. Create the widget.
  - In App drop-down menu, select Gmail app. This way, you'll be able to do `Scheduled Send` in addition to regular `Send`, for reminders that you want to appear later.
  - Put your email in the To field.

Create a reminder:
1. Tap widget icon
1. Tap Subject
1. Enter Reminder
1. If you want the Reminder to show up right away: Tap Send icon
1. If you want to schedule for later, use the `Scheduled Send` feature. Tap on
3-dot menu on upper right and select `Schedule Send`.

*Extra work compared to Inbox*
Step 2), which is one tap. (Unfortunately [this step is required](https://jeffangelini.com/EmailYourself/faq/).)

### Repeating reminders

Inbox had repeating Reminders. With Gmail, emails can't snoozed repeatedly. So instead, I create repeating
calendar events. I get an email notification for each event.

Ideally I could use [JR's IFTTT applet](https://user-images.githubusercontent.com/10929390/63665526-8e616680-c780-11e9-9f9f-be9e7592b4cb.png).
But I couldn't get that working. I keep getting "There was a problem with the
Gmail service.". (I've tried deleting and recreating the Google Calendar/Gmail
service connections, recreating the applet, etc.)

Then I tried Zapier. That worked, but the emails had a lot of boilerplate.

So finally I settled on Google Calendar notification emails, with AppsScript to delete the boilerplate.

Defualt Google Calendar notification emails:

![default calendar email](https://user-images.githubusercontent.com/10929390/63405051-55994a00-c39a-11e9-96f3-d623cfdbdb83.png)

I use an Apps Script to forward the email, getting rid of the boilerplate:

![forwarded email](https://user-images.githubusercontent.com/10929390/63405052-55994a00-c39a-11e9-954f-daa9d82e45c0.png)

One-time setup:
1. If you don't already have one, acquire a secondary Google account. This is so you don't
clutter the calendar of your primary account.
1. Go to Calendar settings for the secondary account. For `Event notifications`, set default to `Email 1 minutes`. For `All-day event notifications`, set default to `Email 0 days before at 6:00am`.
1. Go to script.google.com and create a new script with:
    ```
    function forwardCalendarEvents() {
      var threads = GmailApp.getInboxThreads();
      for (var i = 0; i < threads.length; i++) {
        var message = threads[i].getMessages()[0];
        if (message.getFrom().indexOf("calendar-notification@google.com") != -1) {
          // For events with long names, Calendar truncates event name in email subject (with "...")
          // So grab event name from email body
          var body = message.getBody();
          var begin = body.indexOf('<span itemprop="name">') + '<span itemprop="name">'.length;
          var end = body.indexOf('</span></h3>');
          var eventName = body.substring(begin, end);
          GmailApp.sendEmail('MYEMAIL', eventName, /* body */ '', {name: 'Reminder'});
          threads[i].moveToArchive();
        }
      }
    }
    ```
    Replace `MYEMAIL` with your primary email address.
1. `Edit` menu -> `Current project's triggers` -> `Add Trigger`. Create a time-based trigger that runs every hour.
1. For each repeating reminder, create a repeating calendar event for your secondary account.
1. Optional: Change secondary account's name to `Reminder` and change profile
picture to [Inbox Reminder icon](https://miro.medium.com/max/64/1*iiTH-aEmmXAi9bmv-H9JPA.png) (courtesy of [here](https://medium.com/@adrienjoly/how-to-add-reminders-to-gmail-like-in-google-inbox-f81fb5ef9ab5)).

*Extra work compared to Inbox:* None
