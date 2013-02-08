appstorebyemail (App Store By Email)
===============

## The Problem:

You are on your iPhone or iPad and you hear about this cool new Mac App Store app, so you google it, click the link… and find yourself staring at a page that tells you that you can only see Mac App Store apps on a Mac.

*Or* you're on your iPhone and hear about a cool new iPad app, but it's iPad-only, and so you can't see anything about it either.


## The Solution:

Take that link, email it to yourself, have your Mac do some computational 'magic' and send you back a nice HTML email showing you information from the Mac App Store (or iOS App Store).

## Warning:

This isn't something you can just drop in to your system and have it work. Expect to take some time to configure things on your end.

That said: none of this is *difficult*. It shouldn't take more than an hour to set it all up. (Don't be put off by the fact that my instructions are… "verbose". I've done all the hard parts already, now you just have to put them into the right place in your system.)

However, once you have it up and running, it only takes about a minute from the time I send the email until the time I get the response.

(It even works if you get one of those ugly Google.com search result links which wants to redirect you before taking you to the Mac App Store page.)

## This Is What You Will Need:

1. A Mac, which has Mail.app running on it and which will automatically fetch and process your email.

2. `multimarkdown` and `lynx` and `msmtp` which you can get via [Homebrew](http://mxcl.github.com/homebrew/).

3. `html2text.py` which you can get from <https://github.com/aaronsw/html2text/downloads>



## There is another alternative!

I have also done this using [Hazel](http://www.noodlesoft.com/hazel), [Drafts for iOS](https://itunes.apple.com/us/app/drafts/id502385074?mt=8), and Dropbox. It still requires a Mac for processing, but it does not require that Mail.app be running. If you're interested in that, go to <https://github.com/tjluoma/drafts2mas>.

## Setup (an overview)

1. Install msmtp and configure it. I wrote a [how-to  at TUAW.com](http://www.tuaw.com/2010/05/04/msmtp-a-free-tool-to-send-email-from-terminal/). It should take less than 30 minutes if you already have Homebrew installed. Maybe 10 if you're quick.

2. Install `multimarkdown` and `lynx` too.

3. Install [html2text.py](https://github.com/aaronsw/html2text/downloads) somewhere in your $PATH

4. Move mail-reply-with-mas-info.sh to somewhere like /usr/local/bin/ *(If you move it somewhere else you'll need to edit `Mail-App-Reply-With-MAS-Info.scpt` to tell it where to find it.)*

4. Move the `Mail-App-Reply-With-MAS-Info.scpt` file to ~/Library/Application Scripts/com.apple.mail/

5. Configure a Mail.app rule to trigger Mail-App-Reply-With-MAS-Info.scpt when the right criteria are met. Mine is below.


5. Move `basic-clean-reformat.rc` to `/usr/local/etc/tidy/basic-clean-reformat.rc`

6. In `mail-reply-with-mas-info.sh` you'll need to:   
	* verify HTML_TIDY is pointing to wherever you saved  `basic-clean-reformat.rc`
	* set your PATH
	* set the DIR variable (where do you want files saved?)
	* (All 3 of those are right at the top)


## Mail.app Rule

![](https://raw.github.com/tjluoma/appstorebyemail/master/mail-rules.jpg)

My rule is fairly simple, it looks to make sure that the Subject line begins with either http:// or https:// OR is sent to my super-secret email address. You may want to make your criteria different, just make sure you at least have the first two.

I move the message to make sure it isn't match again, I mark it blue to make sure that I know it matched this rule, and then I run the AppleScript described above.

The AppleScript does something very simple: it looks for the Reply-To address of the person who sent the email, and it takes the Subject: line, and it sends both of those to the `mail-reply-with-mas-info.sh` shell script.

The shell script then does all of the rest of the work, including actually sending the reply.

## How to test it

* Make sure `msmtp` is setup properly. In Terminal.app you should be able to type:

	echo "This is a test" | Mail -s "Test at `date`" you@example.com

(except that you should use your actual email address instead of 'you@example.com')

If that email is delivered successfully, continue. 

If not, check msmtp and make sure that you have the ~/.msmtprc file setup properly.

* Next you should test the script itself without going through Mail.app. To do this, go to Terminal.app again and type:

	mail-reply-with-mas-info.sh 'https://itunes.apple.com/us/app/coderunner/id433335799?mt=12' 'you@example.com'

When that delivers, you should have the information for the [CodeRunner](https://itunes.apple.com/us/app/coderunner/id433335799?mt=12) app.

* If that works, then try sending an email to whatever address you have setup to receive these in Mail.app.

Remember: The only thing that matters is the Subject: line, which must contain a URL to a Mac App Store or iOS App Store app. (The body of the email is ignored.)

