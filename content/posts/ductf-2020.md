---
title: "Writeup: Down Under CTF 2020"
date: 2020-09-19T09:28:04-04:00
categories:
- writeup
tags:
- ctf
- security
---

This past weekend, my friends and I had the chance to participate in [Down Under CTF](https://downunderctf.com/) 2020, a CTF aimed at Australian secondary- and tertiary-school students. Though our team didn't come anywhere close to placing, or even completing all the challenges, we had a fun time and learned some new techniques for our next CTF.

This writeup is a team effort by me, [Dean Jiao](https://twitter.com/deanjiao), and [Ruju Jambusaria](http://github.com/rujuj), and was written while we were solving challenges. It is *not* an exhaustive writeup (please consult [the organizers' writeup](https://github.com/DownUnderCTF/Challenges_2020_public) for a full writeup). Instead, this writeup is a collaborative effort in exploring dead ends, throwing ideas on the wall, and seeing what sticks. 

Challenges we made a decent attempt at completing (or successfully solved) are therefore included below; other challenges are not.

# Background
Down Under CTF 2020 ran from Friday, September 18 5AM EDT to Sunday, September 20 5AM EDT (7PM Australian Eastern Time). There were [over 3000 participants](https://twitter.com/DownUnderCTF/status/1307651854134734848), and over 1000 teams on the scoreboard.

Our team was called "why not make team" (... this is a long story), and consisted of me, Dean, and Ruju. If you're curious about how we did overall, you can checkout [our team stats](#conclusion) near the end.

There were seven challenge categories, with difficulties ranging from "sanity check" to "insane".
1. [OSInt](#osint)
2. [Forensics](#forensics)
3. [Web](#web)
4. [Crypto](#crypto)
5. [Misc](#misc)
6. [Pwn](#pwn)
7. [Reversing](#reversing)

# OSInt
## Petstagram
**Difficulty**: Beginner  
*Who is Alexandros the cat exactly? And who is this mysterious "mum" he keeps talking about?  
Submit his mum's full name in lowercase and with underscores instead of spaces, as the flag: DUCTF{name}*

Given the challenge name, searching on Instagram for Alexandros the cat seemed like a good start. It brought us to [this profile](https://www.instagram.com/alexandrosthecat/):
![Screenshot of Instagram profile for alexandrosthecat. There are two posts, a black and white photo of a cat and a coloured photo of (presumably) the same cat with a red bow. The account has 9 followers and is following no one.](/img/ductf-2020/petstagram-alexandros.png)

Dipping into the followers, we then found [this person, Emily Waters](https://www.instagram.com/emwaters92/):
![Screenshot of Instagram profile for emwaters92. There are two posts, a video where the current frame is black and the same black and white cat photo from Alexandros. The accounts has 9 followers and is only following 1 account.](/img/ductf-2020/petstagram-emwaters92.png)

Whom we suspected to be the "mum" referenced. However, entering `DUCTF{emily_waters}` didn't work, and we realised there was a middle name from the email:
```sh
emilytwaters92@gmail.com
```

Sending an email to that email address got us an out-of-office reply:
![Email to emilytwaters@gmail.com. Subject line: "Business inquiry: middle name". Body: "Hi there! I love your cat Alexandros! Such a cutie. I have a business inquiry regarding a particular flag. Do you mind if I ask you what your middle name is?"](/img/ductf-2020/petstagram-og.jpg)
![Email reply from emilytwaters@gmail.com. Subject line: "Out of Office from September 6th Re: Business inquiry: middle name". Body: "Hello, I will be out of the office from Sunday, September 6th onward. If you need immediate assistance during my absence, that sounds like a you problem. Otherwise, I will probably respond to your emails as soon as possible if I return. Kind regards, Emily Theresa Waters."](/img/ductf-2020/petstagram-reply.png)

Which gave us the flag in the signature.

**Flag**: DUCTF{emily_theresa_waters}

## Bad Man
**Difficulty**: Easy  
*We have recently received reports about a hacker who goes by the alias und3rm4t3r. He has been threatening innocent people for money and must be stopped. Help us find him and get us the flag.*

Googling `und3m4t3r` or searching for the `und3rm4t3r` user on Discord revealed nothing.

*Post-CTF*: Turns out we were supposed to look for [them on *Twitter*](https://twitter.com/und3rm4t3r) instead. 

One of their Tweets mentioned "deleting a Tweet that contained personal information", which had been conveniently snapshotted [on the Wayback Machine](http://web.archive.org/web/20200723112257/https://twitter.com/und3rm4t3r):
![Screenshot from the Wayback Machine of und3rm4t3r's profile. Their profile picture is a man in a hoodie, with only the whites of the eyes showing. Their bio says "This is an account made for a Capture the Flag competition". The most recent Tweet shows the flag](/img/ductf-2020/bad-man-wayback.png)

**Flag**: DUCTF{w4y_b4ck_1n_t1m3_w3_g0}  
**Tools**: [Wayback Machine](http://web.archive.org/)

##  I think this one is really going to take off
**Difficulty**: Medium  
*You'll never believe it but I swear I saw one of those big American refueling planes flying around right over the boxing croc on the first of September. If only I knew its registration number... I bet I'd be able to find out all kinds of information, like the day it first flew!  
The flag will be the date of the first flight of the plane I saw in this format: DUCTF{DD-MM-YY}*

*Fun fact*: we were the 10th team to solve this one!

We started with Googling the "boxing croc", since we all had no idea what it was:
![Screenshot of Google info card on the "boxing croc", or rather, the "Big Boxing Crocodile". There is a photo of a crocodile statue wearing boxing gloves, as well as a screenshot of Google Maps showing the boxing croc's location. It is a tourist attraction in Humpty Doo, Australia, and its address is 326 Arnhem Hwy, Humpty Doo NT 0836, Australia.](/img/ductf-2020/take-off-boxing-croc.png)

Looking at a map, we noticed that Darwin International Airport was nearby, so it was likely that any aircraft going over the Boxing Croc was probably heading into or out of Darwin.
![Screenshot of Google Maps showing Darwin and the boxing croc](/img/ductf-2020/take-off-darwin-map.png)

Next, we looked at a list of American refueling planes, hoping to find a model so we could find flight data for them. This was inconclusive, especially since flight trackers only showed flights from the past 7 days.

We also found [this totally not sketchy site](http://www.planeflighttracker.com/2014/04/united-states-military-aircraft-in.html) for finding the model codes for military aircraft. 
![Screenshot of United States NATO Military Aircraft Tracker and which aircraft are in operation.](/img/ductf-2020/take-off-aircraft-tracker.png)

However, a whole bunch of googling later, we still found nothing. 

We happened upon an article about an [Australian/American Joint Air Force exercise](https://www.pacom.mil/Media/News/News-Article-View/Article/2336270/increasing-interoperability-b-2s-b-1s-join-us-marine-corps-australian-defence-f/) that happened near the given date, and it mentioned a particular refueling plane, the Boeing KC-135R Stratotanker. with model number K35R. 

Still, flight trackers had no historical data going that far back, so we had to get a 7-day free trial for [RadarBox](https://www.radarbox.com/). Searching for all flights with the model K35R, we looked for flights around September 1st.
![Screenshot of flight list around September 1st](/img/ductf-2020/take-off-flight-list.png)

We eventually found a flight whose path looked like it went over where Boxing Croc would be:
![Screenshot of flight PEARL02 which curves over to Darwin](/img/ductf-2020/take-off-darwin-flight.png)

Googling the plane registration number: `58-0086` then led to 
[https://planefinder.net/data/aircraft/58-0086](https://planefinder.net/data/aircraft/58-0086), revealing the final flag.

**Flag**: DUCTF{16-07-59}  
**Tools**: [Google Maps](https://maps.google.com), [Plane Flight Tracker](https://www.planeflighttracker.com), [RadarBox](https://www.radarbox.com/), [Plane Finder](https://planefinder.net/)

## A turn of events w/ some REALLY bad OPSEC
**Difficulty**: Hard  
*We have identified the key suspect in multiple crimes, with authorities in pursuit. The suspect has been placed on the Interpol red list, and is believed to have already escaped the country through some unknown form of transport. Locating the transport the suspect took will be vital, and with lives on the line, time is of the essence. Provided attached is the evidence collected in this case.*  
*When you have determined which transport the suspect took, please submit it to us in the form of a flag containing the transports call sign and name, all uppercase, with any spaces replaced by underscores. Flag format: DUCTF{callsign_name}*

Given the image provided, we figured out a lot of the base information required:
- Female
- Born September
- Last location: Netherlands (?) - *Post-CTF*: not true
- MAC address `f8:ab:05:cf:37:54` is issued [from this company](https://maclookup.app/macaddress/F8AB05)
- Using a burner phone
- Gelato
- Might be Owner - Cat's name. Alexandros and their mum is mentioned in Petstagram.
- Plans to leave on Sept 18 2020 via [???]

Having solved Petstagram, we had access to [the second post on emwaters92's account](https://www.instagram.com/p/CEzKUbeAMG-/): a video with beeping in the background, which sounded like a T9 phone keyboard. We also knew that her location tag in this video was the Gelateria on the Docks, near Melbourne's ports.

Using a [DTMF tone converter](http://dialabc.com/sound/detect/), followed by a [T9 Decoder](https://www.dcode.fr/t9-cipher), we were able to get the message:
```sh
6338063028090300
MEET ME AT 9:30
```

Unfortunately at this point we stopped, thinking we didn't have much of a lead. We noticed that the gelato place was not open at 9:30, and there were tramboat cruises available nearby, but we had no idea what a "call sign" was.

*Post-CTF*: However, had we gone a little further and noticed the Port of Melbourne nearby, we could have obtained a list of ships that were passing through on or after 9:30AM. This would've eventually led us to find the call sign of the ship that Emily boarded.

We also could've used the MAC address on [Wigle](https://wigle.net), which could have confirmed Emily's location and perhaps pointed us to the Port of Melbourne more closely. 

**Flag**: DUCTF{SWHR_CAP_VICTOR}  
**Tools**: [DTMF tone converter](http://dialabc.com/sound/detect/), [T9 Decoder](https://www.dcode.fr/t9-cipher), [Wigle](https://wigle.net)

## Off the Rails
**Difficulty**: Hard  
*I'm in trouble, I'm on the run... My friend told me he would meet me underneath a rail bridge 2km from his house before we escape on a train but he forgot to tell me where he lived.  
He took two day trips relatively close by and sent me two photos. I also know that late last year, there was a fire near the suburbs approximately 25km from where he lives.  
He lives somewhere in the middle of those three points. Could you tell me the name of the road parallel to the bridge, and the latitude and longitude of the bridge?  
P.S. The longitude and latitude should be to 3 decimal places (plus if necessary include the . and/or -). Each segement of the flag should be seperated by an underscore. The road name type should be the same as when you find it. The flag is also case insensitive.  
Example: DUCTF{latitude_longitude_nameofroad_roadnametype}*

Using the bridge image provided, we were able to find out the type of bridge on Google with a reverse image search.
![Screenshot of Google reverse image search, showing "trestle" as the recommended search term](/img/ductf-2020/otr-trestle.png)

With this in mind, and knowing the fire tidbit, we began to search for bridges that had had fires nearby. The list we came up with was:
- Noojee
- Monbulk
- Wairewa
- Stony Creek

However, none of the coordinates/road names that we found parallel to the bridges were the correct flag, so we stopped there.

*Post-CTF*: What we forgot to use was the picture of the koala, which apparently had EXIF data to be extracted using `exiftool`. This would have helped create the triangle necessary to find the correct bridge and coordinate spot.

As it turns out, one of the bridges we had researched (Monbulk) *was* actually the correct bridge, but not having the other pieces, we were unable to successfully triangulate the correct train line necessary.

# Forensics
## On the spectrum
**Difficulty**: Beginner  
*My friend has been sending me lots of WAV files, I think he is trying to communicate with me, what is the message he sent?*

The WAV file that's given sounds like white noise with some patterns:
![Initial wave spectrum](/img/ductf-2020/spectrum-initial.png)

After some serious noise cancellation in [Audacity](https://www.audacityteam.org/), it started to look like something.
![Noise-cancelled audio waveform](/img/ductf-2020/spectrum-noise-cancel.png)

Uploading this `.wav` into an [online spectrum analyzer](https://academo.org/demos/spectrum-analyzer/) (like - on the spectrum? get it?) then yielded us the flag.
![Finaly spectrum analysis, that includes the flag as text](/img/ductf-2020/spectrum-analysis.png)

**Flag**: DUCTF{m4by3_n0t_s0_h1dd3n}  
**Tools**: [Audacity](https://www.audacityteam.org/), [Spectrum Analyzer](https://academo.org/demos/spectrum-analyzer/)

## i love scomo
**Difficulty**: Medium  
*I really do love Scott Morrison! <3 <3 <3  
However, some people don't like me because of my secret crush :(. So I have to hide my secrets using steganography. This is my hidden space, where I can dream about being with Scomo and I really appreciate that no one tries to reveal my secret message for him.*

We tried a combination of different tactics. At first we tried using `binwalk` through Cyberchef to extract files, and also looking at the image through `strings` and `xxd`. However, neither of these attempts were particularly fruitful.

Going back to the "extract files" idea, though, we tried again on a different day with `steghide` this time. We found that there was a password and tried a variety of "Scomo-loving" passwords to crack it:

- Scott Morrison
- Scott_Morrison
- scomo
- Scomo
- ilovescomo
- I_love_Scott_Morrison
- I love Scomo
- I_love_Scomo
- sco mo
- Sco Mo
- Sco mo
- sco_mo
- Sco_mo
- ilovesco_mo

All of which did not work. At this point, we gave up.

*Post-CTF*: What we didn't know, was that the password was even simpler than that, and that there is a "common passwords" list called `rockyou`.

Using a steganography cracker called `stegcracker` with `rockyou` could then reveal the real password: `iloveyou`. (I know - how did we not think of that!)

The `secret_message.txt` file produced then is just the Australian national anthem. But apparently the extra spaces take advantage of whitespace steganography.

If there is a trailing space at the end of the line, we added a "1"; otherwise, we added a "0". However, the file was also 462 lines long, so it was easier to just script it.
```python
with open('secret_message.txt', 'r') as f:
    msg = ""
    lines = f.readlines()
    for line in lines:
        if line == "\n" or line[-2] != " ":
            msg += "0"
        else:
            msg += "1"
    print(msg)
```

Running this script then created the following string (line separated for readability):
```sh
010001000101010101000011010101000100011001111011011010010101111101010010001
100110110110001001100011010010101111101101100001100000011000000110000001100
000100111100110000011011110110111100110000011101100011001101011111001101010
110001100110000011011010011000001111101000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000000000000000000000000000000000000000000000000000000000000000000
000000000000
```

Trimming off the extra 0s at the end, we then can get the flag.

**Flag**: DUCTF{i_R3lLi_l0000O0oo0v3_5c0m0}  
**Tools**: [`stegcracker`](https://github.com/Paradoxis/StegCracker), [`rockyou`](https://www.kaggle.com/wjburns/common-password-list-rockyoutxt), [`steghide`](http://steghide.sourceforge.net/), [image extractor](https://gchq.github.io/CyberChef/#recipe=Extract_Files(true,true,true,true,true,true,false,true)), `strings`, `xxd`  
**Further reading**: [whitespace steganography](http://manpages.ubuntu.com/manpages/bionic/man1/stegsnow.1.html), which can apparently count spaces and/or tabs, and in particular `stegsnow`

# Web
## Leggos
**Difficulty**: Beginner  
*I <3 Pasta! I won't tell you what my special secret sauce is though!*

Navigating to the given page gives us this:
![Screenshot: title: "My Second Favourite Pasta Sauce". Under: "This is my second favourite pasta sauce! I have safely hidden my favourite sauce!" with a picture of a jar of Leggo's pasta sauce.](/img/ductf-2020/leggos-home.png)

Right-clicking and trying to use `Ctrl+U` to view the source give a popup that simply says "not allowed". However, `Ctrl+Shift+I` still works, allowing us to navigate to "Sources" and look at `disableMouseRightClick.js`, giving us the flag.
![Screenshot: sources panel on the inspector, showing the contents of `disableMouseRightClick.js`](/img/ductf-2020/leggos-source.png)

**Flag**: DUCTF{n0_k37chup_ju57_54uc3_r4w_54uc3_9873984579843}

## Web Badmin
**Difficulty**: Easy  
*We launched a game and now it is no longer launched :( can you figure out what happened plox. HALP*

The given link takes us to "Epic Website 1", featuring a red banner that says, "GAME STATUS: Broke af", a Youtube video and a button at the bottom that says "Beep Boop".
![](/img/ductf-2020/badmin-home.png)

Looking in the source, we see that there's a commented out link to `https://epicgame.play.duc.tf`.

However, navigating to this site did not work, as it could not be loaded.

*Post-CTF*: Had we looked at the error message more carefully, we would have realised that the specific error message was `DNS_PROBE_FINISHED_NXDOMAIN`, meaning the DNS server could not find a corresponding IP address for this domain.

However, that's not to say there isn't other information on it. Using `dig`, we could run:
```sh
dig epicgame.play.duc.tf TXT
```

Revealing a `TXT` record for the given site containing the flag.

**Flag**: DUCTF{wait_im_confused_what_are_record_types_in_DNS???}  
**Tools**: [`dig`](https://linux.die.net/man/1/dig)  
**Further reading**: [Network steganography with DNS records](https://www.researchgate.net/publication/311333694_Network_Steganography_in_the_DNS_Protocol) (TXT records in particular), is apparently a real thing

## Design Comp
**Difficulty**: Hard  
*Come join our homepage design competition and test out your CSS skills!*

Clicking on the link takes us to a `bland`-looking website with a call for the community to help design their site. At the bottom, there's a link to login:
![Title: "Design our homepage 2020!" with a picture of a bed with fluffy pillow under, from Unsplash. A line of text under that reads: "As you can see, our homepage is currently pretty bland at the moment. We're looking toward the community to design us a new homepage!"](/img/ductf-2020/design-bland.png)

The login screen has only 1 field for the username and after entering one, it leads you here, where you can then enter in a URL for your submission. 
![A white box on a blue background with text. Title: "Welcome rujube". "About" section has a current rating of 8, with a link to rujube's playground. "Make a submission" section has a URL input field, with a CAPTCHA followed by a green Submit button. Underneath, a red Logout button.](/img/ductf-2020/design-login.png)

*Fun fact*: Submitting https://google.com as the URL gives you a score of 8, apparently.

The link to the playground leads to the original `bland` site, but this time with an "editor" button in the corner:
![A similar screenshot to the original homepage; however, this time there is a picture of a mountain at sunset, all warm oranges tones, and an editor button in the top right corner.](/img/ductf-2020/design-editor-bland.png)

The editor is initially blank, but you can fill it in with whatever code you want. Here, Ruju filled it in with the initial page source. 
![](/img/ductf-2020/design-editor.png)

However, it seems that this is invalid. The preview won't load either.

*Post-CTF*: It turns out this challenge was a *lot* more complicated than we'd hoped. Cross-site scripting (through CSS, no less!) was not something we were super familiar with, and reading through [the available writeup](https://github.com/DownUnderCTF/Challenges_2020_public/tree/master/web/design-comp#writeup) made it clear we needed to do a lot of reading.

## Robotsss
**Difficulty**: Medium  
*Us robot devs use better templates than those stupid humans!*

You're redirected to a site for robots, with two cute robots talking under the message "All humans must die". The message also notes you can view the posts after you make an account.
![Note - all text on the page is in capitals. A top, black, menubar has "Secret Robot Forum" in the left and "Logout" in the right. Title says "Welcome to Secret Robot Forum!!". Underneath, it says "Create an account to view the posts. All humans must die." Under that text, a comic of two robots talking, where one robot (presumably) tells a joke in binary, and the other says, "That's hilarious!".](/img/ductf-2020/robotsss-initial.png)

After registering an account, I'm led to a page with 2 posts.
![Two post titles: "Message to the robot rebels" and "Message to the robot devs".](/img/ductf-2020/robotsss-posts.png)

In the second post, the message says the flag is located in: `s3cr3t_p4th/robot_fl4g.txt` but appending that to the URL gives a 404 Not Found error.

However, looking in the source for the two posts, we find two binary strings:
```sh
011010000111010101101101011001010110111000101110011101000111100001110100
0110011001101100001101000110011100101110011101000111100001110100
```

Translating this gives us two paths:
```sh
humen.txt
fl4g.txt
```

Trying `fl4g.txt` as a path doesn't work, but `humen.txt` leads us to the `/Bender` path:
![Propaganda poster of Bender from Futurama, with the text, "Not the leader we want" at the top, and "But the leader we deserve" at the bottom](/img/ductf-2020/bender.jpeg)

Running `exiftool` on this image then gives us this binary string as the "Artist" (line-separated for readability):
```sh
011000010110010001101101011010010110111000111010010101000110100001101001011
100110010110101001001011100110010110101010100011010000110010100101101010000
010110010001101101011010010110111000101101010100000110000101110011011100110
111011101101111011100100110010000101101010110000100010000100001
```

Which, when translated from binary, gives us the actual admin credentials:
```sh
admin:This-Is-The-Admin-Password-XD!
```

Logging in as the admin for the robotsss site now, we come to the "Admin Terminal", which claims to echo anything we type in.
![Title: "You have reached the admin terminal! I can echo anything you type in." Under: a text field labelled "Enter a string" that has sample text "e.g. hi" and a black Submit button. Under the Submit button, it says "You typed:"](/img/ductf-2020/robotsss-admin-echo.png)

And around this point, we had no idea what to do, and it was about 2AM our time (5PM AEST?), so we decided to try as many other challenges as we could.

*Post-CTF*: Apparently this form is vulnerable to server-side template injection, because of Jinja2. Wrapping the contents in `{{ }}` (creating a template) will cause the form to execute the code inside.

So, giving this terminal `{{ config.items() }}` will then spit out:
![A dictionary of config items.](/img/ductf-2020/robotsss-admin-config.png)

Notice how there is an item `SECRET_KEY`, which maps the `app.jinja_env.globals['getFile']` to the function `getFile`, allowing us to get any file using `getFile`. We can then use the terminal once again to run `getFile('fl4g.txt')`, which will then print the flag.

**Flag**: DUCTF{a15c011f-b595-4f7d-bc63-00dc972b3464}  
**Tools**: [Binary decoder](https://gchq.github.io/CyberChef/#recipe=From_Binary('Space')), [`exiftool`](https://exiftool.org/)  
**Further reading**: [Jinja2 server-side template injection](https://medium.com/@nyomanpradipta120/ssti-in-flask-jinja2-20b068fdaeee)

# Crypto
## ROT-i
**Difficulty**: Beginner  
*ROT13 is boring!*

The challenge text is as follows (line-separated for readability):
```md
Ypw'zj zwufpp hwu txadjkcq dtbtyu kqkwxrbvu! 
Mbz cjzg kv IAJBO{ndldie_al_aqk_jjrnsxee}. 
Xzi utj gnn olkd qgq ftk ykaqe uei mbz ocrt qi ynlu, 
etrm mff'n wij bf wlny mjcj :).
```

We ran this text through a few ROT decoders, but weren't able to get anything sensible out of it. However, looking again at the structure of the sentences, we knew that the flag would have to be in the format of `DUCTF{...}`. There was only one place in the text that had this pattern:
```sh
IAJBO{ndldie_al_aqk_jjrnsxee}
```

It seemed impossible for all the letters to be shifted the same way, but I pointed out that there could possibly *multiple* ciphers, i.e. each letter was shifted a different amount. Ruju wrote out the messages and noticed the same thing - in fact, we noticed that the ciphers increased by 1 each time (i.e., had a shifting `i`).
![On dot-grid paper: "IAJBO" with "DUCTF" mapped under it. Below, two written out alphabets with pairs of letters shifted. Below that, math: D to I = 5, A to U = 6, J to C = 7, B to T = 8, O to F = 9.](/img/ductf-2020/roti-shift.jpg)

With this in mind, we set to counting out some letters to form the flag. It took us a couple tries, but we ended up with:
```sh
DUCTF{crypto_is_fun_kjqlptzy}
```

Which looked like it *had* to be a mistake. (What the hell is `kjqlptzy`?)

But it wasn't. 

**Flag**: DUCTF{crypto_is_fun_kjqlptzy}

# Misc
## Twitter
**Difficulty**: Sanity check  
*Check out our Twitter! Find the post with the flag! You can give us a follow if you like <3*

Going on the [DUCTF Twitter account](https://twitter.com/downunderctf), we scroll down and see their first Tweet is:
![A Tweet from the DUCTF Twitter account on July 25: "RFVDVEZ7aHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g/dj1YZlI5aVk1eTk0c30="](/img/ductf-2020/ductf-tweet.png)

Converting `RFVDVEZ7aHR0cHM6Ly93d3cueW91dHViZS5jb20vd2F0Y2g/dj1YZlI5aVk1eTk0c30=` from Base64 (the equals sign giving it away) - we then get the flag.

**Flag**: DUCTF{https://www.youtube.com/watch?v=XfR9iY5y94s}  
**Tools**: [Base64 decoder](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true))

## Discord
**Difficulty**: Sanity-check  
*JOIN OUR DISCORD!*

Looking in the description of the #general channel, it's pretty obvious what the flag is.
![A screenshot of the Discord top bar, showing the general channel and the flag right next to it](/img/ductf-2020/ductf-discord.png)

**Flag**: DUCTF{c0n6r475_y0u_h4v3_n0w_j01n3d_0ur_4m4z1n6_d15c0rd}

## homepage
**Difficulty**: Beginner  
*Hmm wasnt the homepage logo rainbow before? I wonder how it works...  
https://downunderctf.com*

This was by far the most frustrating challenge in the entire CTF for us, purely because:
1. It was marked as a "beginner" challenge, and we'd definitely solved harder
2. We started it on Friday evening (when we'd started working on this) and didn't solve it when we went to sleep on Saturday night (when we'd stopped working on this)
3. The organizers released a hint around halfway through the CTF which was quite possibly less helpful than the challenge description (which was also modified early on to point specifically to the colours of the given map, rather than "visible spot on the homepage").

Let's begin.

The Down Under CTF website has this beautiful map of dots that forms Australia, and when mousing over the dots, they change colour and form this image:
![A map of Australia made dots that neatly fall into a grid. Some dots are a darker teal, while other dots are a pale blue-green.](https://codimd.s3.shivering-isles.com/demo/uploads/upload_12bb31f827a527e488c1d0d720f7d95d.png)

Looking into the source code of the map, we see that the colours are controlled by a script called `splash.js`, using this string:
```javascript
const lol = "00101000000010000010111101100001101001011101000101101110100001100011111101101010" +
        "1010001111000111101000110010000001000000010010001000011111011101001111101001110111101010" +
        "0110011110010111100011011110001010000010001100110110000101011001110101010001011101001001" +
        "0110000011011110001010110011001011111001110010011101100011110000110111111001000011010101" +
        "0100000000101000111110101000111001111100111000010001000100110";
```

Here are the things we tried:
* Translating it to ASCII
* Translating it to hex
* Adding `lol` as "101" to the beginning or end of the string (to make it split evenly into 8-bit binary) and then translating it

None of these yielded the flag. 

At some point, the organizers revealed my least favourite hint ever, which was:
```sh
01101000 01100001 01110110 01100101 00100000 01111001 01101111 01110101 
00100000 01100011 01101111 01101110 01110011 01101001 01100100 01100101 
01110010 01100101 01100100 00100000 01100010 01101001 01101110 01100001 
01110010 01111001 00111111
```

Or, `have you considered binary?`.

*Damn, ya think? Ya think I haven't considered binary yet?*

*Post-CTF*: The very beginning of the official writeup for this challenge states:
```md
thought that this would be a fun challenge for people starting out! 
on second thoughts, it mightve been highly annoying and not "beginner"
```

I agree. It *was* highly annoying.

Overall, the technique required was to order the dots from top to bottom, left to right. Since each dot mapped to an index in the `lol` string, the `lol` string had to be reordered as well. Then, the binary string can be translated to ASCII.

**Flag**: DUCTF{i_turn3d_mysE1f_inT0_s0m3_f1ag}  
**Tools**: [Binary decoder](https://gchq.github.io/CyberChef/#recipe=From_Binary('Space'))

## Welcome!
**Difficulty**: Beginner  
*Welcome to DUCTF!*

This challenge asked us to `ssh` using these credentials:
```sh
ssh ductf@chal.duc.tf -p 30301
password: ductf
```

Which then led to this beautiful screen.
![Screenshot of very colourful terminal that is almost full. Each colour is the phrase "Welcome to DUCTF!" or the flag. ](/img/ductf-2020/ductf-terminal.png)

The phrase "Welcome to DUCTF!" kept popping up, filling the terminal and overwriting past phrases as well.

However, if you look closely, there is a flag in some of them (you can spot them because they have the curly braces). It took a couple tries to really read the whole thing, but we got there in the end.

**Flag**: DUCTF{w3lc0m3_t0_DUCTF_h4v3_fun!}

## 16 Home Runs
**Difficulty**: Beginner  
*How does this string relate to baseball in anyway? What even is baseball? And how does this relate to Cyber Security? ¯(ツ)/¯*

The given string is:

```sh
RFVDVEZ7MTZfaDBtM19ydW41X20zNG41X3J1bm4xbjZfcDQ1N182NF9iNDUzNX0=
```

With the equals sign at the end, the string is a pretty dead giveaway for Base 64 encryption. Running it through a decryptor we get the flag.

**Flag**: DUCTF{16_h0m3_run5_m34n5_runn1n6_p457_64_b4535}  
**Tools**: [Base64 decoder](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true))
## in a pickle
**Difficulty**: Easy  
*We managed to intercept communication between und3rm4t3r and his hacker friends. However it is obfuscated using something. We just can't figure out what it is. Maybe you can help us find the flag?*

Looking at the data we get:
```sh
(dp0
I1
S'D'
p1
sI2
S'UCTF'
p2
sI3
S'{'
p3
sI4
I112
sI5
# more lines go here
S'}'
p4
sI24
S"I know that the intelligence agency's are onto me so now i'm using ways to evade them: I am just glad that you know how to use pickle. Anyway the flag is "
p5
s.
```

So it looks like the middle bits between the `S'{'` and `S'}'` were about where the flag would sit. We weren't sure what to do with this, but with the line `how to use pickle` and the obfuscation note from the challenge description, we did some Googling around obfuscation and `pickle`.

[`pickle` is a Python library](https://docs.python.org/3/library/pickle.html) used to (de-)serialize Python objects, where the objects are turned into bytestreams and vice-versa. However, this didn't really help us until we found [this StackOverflow question](https://stackoverflow.com/questions/41005412/how-to-turn-pickle-output-into-an-unreadable-format):
![Heres [sic] a snippet from a save file i created with pickle (it's a game): S'Strength' p4 I5 sS'Health' p8 I100](/img/ductf-2020/in-a-pickle-so.png)

Where the data was structured almost exactly like ours! That's when we realised we'd been data that had been pickled, i.e. been the result of `pickle.dumps()`, which means all we had to do was run the reverse:
```python
import pickle

with open('data', 'r') as f:
    read_data = f.read()
    print(pickle.loads(bytes(read_data, 'utf-8')))
```

This then spat out:
```sh
{1: 'D', 2: 'UCTF', 3: '{', 4: 112, 5: 49, 6: 99, 7: 107, 8: 108, 9: 51, 
10: 95, 11: 121, 12: 48, 13: 117, 14: 82, 15: 95, 16: 109, 17: 51, 18: 53,
 19: 53, 20: 52, 21: 103, 22: 51, 23: '}', 24: "I know that the 
 intelligence agency's are onto me so now i'm using ways to evade them: I 
 am just glad that you know how to use pickle. Anyway the flag is "}
```

Which strings together:
```sh
DUCTF{112499910710851951214811784951095153535210351}
```

Which is still not really the flag. Using [an ASCII table](http://www.asciitable.com/) for reference, we can then reference the ASCII values and convert each letter to create the flag.

**Flag**: DUCTF{p1ckl3_y0uR_m3554g3}  
**Tools**: Python (`pickle`), [ASCII table](http://www.asciitable.com/)
##  Tim Tams
**Difficulty**: Easy  
*When I eat too many Tim Tams, I get rather slow!  
WARNING You will want to turn down your audio for this one!*

Deciphering the audio file for this was awful. `Clive.wav` is an incomprehensible mess. It's like a stack of multiple different noise frequencies. We tried to mute certain pitches or slow down the audio file but no new info was gained.

Thinking back to [On the Spectrum](#on-the-spectrum), we tried running it through a spectrogram analyser, which was pretty but still told us nothing:

![Periodic purple shifting to blue, with a cluster of red/orange/white consistently along the bottom](/img/ductf-2020/timtam-spectrum.png)

*Post-CTF*: The reason I'm including this one in our writeup is because the actual solution seemed so far from our realm of imagination I want to remember it forever.

Also, Dean really, really liked looking up information about [Clive Palmer and Tim Tams](https://twitter.com/clivefpalmer/status/1040396840858869760?lang=en) (and Dean and Ruju had never had Tim Tams before).

The way to decode this `.wav` file was not by treating it as audio, but thinking of it as visual instead. The official writeup pointed us to [Slow-scan television](https://www.wikiwand.com/en/Slow-scan_television), where audio could transmit images.

Using [Blackcat SSTV](https://www.blackcatsystems.com/software/sstv.html), a tool linked in the writeup, we were then able to recreate this beautiful image:
![(Presumably) Clive Palmer poses with a thumbsup in front of a map of Australia coloured with the Australian flag. He stands next to text, "Make AUSTRALIA GREAT!!" where "Great" is also underlined, with a huge checkmark underneath that is left of his thumbsup. In the top left corner, a flag looking thing.](/img/ductf-2020/tim-tams-clive.jpg)

Which, of course, does not have the real flag written in it. Taking the text at the top left corner `QHPGS{UHZOYR_Z3Z3_1BEQ}`, and running it through a ROT13 decoder, we get our *actual* flag.

Which is really, really fitting.

**Flag**: DUCTF{HUMBLE_M3M3_1ORD}  
**Tools**: [Blackcat SSTV](https://www.blackcatsystems.com/software/sstv.html), [ROT13 decoder](https://gchq.github.io/CyberChef/#recipe=ROT13(true,true,13))  
**Further reading**: [Slow-scan television](https://www.wikiwand.com/en/Slow-scan_television)
## Addition
**Difficulty**: Easy  
*Joe is aiming to become the next supreme coder by trying to make his code smaller and smaller. His most recent project is a simple calculator which he reckons is super secure because of the "filters" he has in place. However, he thinks that he knows more than everyone around him. Put Joe in his place and grab the flag.*

Navigating to the page given shows a calculator, as described:
![Text: "My mega calculator can do any calculations python will let you do. Use * + / % to do complex problems if you want." Below: an input field labelled "Calculation" with a button "Calculate for me". Under that, "Enter data and the results will be shown here!"](/img/ductf-2020/addition-home.png)

We tried a few things at first, like causing an error:
![Inside the text field is "admin++". Below, the "Enter data" text has been replaced with "You caused an error... because my friend told me showing errors to hackers can be problematic I am not going to tell you what you broke](/img/ductf-2020/addition-error.png)

Or entering in a calculation that results in 0:
![The page looks exactly the same as the initial shot, except with "1-1" inside the text field.](/img/ductf-2020/addition-zero.png)

But after doing more research into the `eval()` function in Python - which was most likely doing this calculation work, we realised that it could probably be used to run some shellcode (ish). So we decided to use the `subprocess` module, which allows you to run shell code in Python.
```python
__import__('subprocess').getoutput('ls')
```

Then gave us this:
```sh
__pycache__ main.py prestart.sh templates test.txt
```

Then, printing out `main.py` via 
```python
__import__('subprocess').getoutput('cat main.py')
```

Printed out the whole script: 
![The entire main.py script that powers the site. A notable variable is "maybe_this_maybe_not", whose value is the flag.](/img/ductf-2020/addition-cracked.png)

Which shows the flag.

**Flag**: DUCTF{3v4L_1s_D4ng3r0u5}  
**Further reading**: [Dangerous Python functions, like `eval()` and the `subprocess` module](https://www.kevinlondon.com/2015/07/26/dangerous-python-functions.html)

## Koala Habitat
**Difficulty**: Easy  
*What an Aussie Banger!  
Flag Format: STRING you end up with after solving challenge --> Spaces separate the words  
NO DUCTF{} required*

Upon first listen, it seemed like the audio file was morse code with some severe distortions. Dean later described it as "bass-boosted peaky audio", which is basically the same thing with fancier words.

On our first attempts, we tried to transcribe the morse code without manipulating the file, but this was seriously tedious and draining. (The exact words I used were "joyce is really not that patient".)

*Fun fact*: The distorted song in the audio file is [Give Me a Home Among the Gumtrees](https://www.youtube.com/watch?v=MLWzPQmd5sc).

A few attempts later, Dean ported the file into [Audacity](https://www.audacityteam.org/) (our old friend!) and noticed the 600Hz tone (the morse code beep) was really prominent, and equalized the audio so that that tone could be isolated.
![A graph with a large bar at 600Hz, outlined in blue.](/img/ductf-2020/koala-equalization.png)

Then it was just a matter of reading morse from this mess.
![Two waveforms of audio](/img/ductf-2020/koala-final.png)

Which still took a while. Don't get me wrong. (My friends are more patient than I.) But we ended up with this:
```sh
-.-. .- .-. .- -- . .-.. .-.. --- / -.- --- .- .-.. .- ... / 
.- .-. . / - .... . / -... . ... - / -.- --- .- .-.. .- ...
```

Which, when translated from morse code, gave us the flag.

**Flag**: CARAMELLO KOALAS ARE THE BEST KOALAS  
**Tools**: [Audacity](https://www.audacityteam.org/), [morse code decoder](https://gchq.github.io/CyberChef/#recipe=From_Morse_Code('Space','Forward%20slash'))

*Fun fact*: Caramello koalas are actually [*chocolates*](https://www.wikiwand.com/en/Caramello_Koala)! They're koala chocolates with caramel on the inside. Now I want some. They're [$20+ on Amazon.ca](https://www.amazon.ca/Cadbury-Caramel-Koala-240g/dp/B002X0QG58) though ...

# Pwn
## Shell this!
**Difficulty**: Beginner  
*Somebody told me that this program is vulnerable to something called remote code execution?*   
*I'm not entirely sure what that is, but could you please figure it out for me?*

Looking at the pieces of code that are provided, this looks like a buffer overflow challenge. We're allowed a buffer of 40 characters, and there's already a function for us to get a shell. If we overflow the buffer just the right amount, we should be able to get a shell.

With some trial and error, we eventually got it to hang at the length of 56 characters. Any more and we'd get a segmentation fault, getting immediately booted out of the server; any less and we'd have an "incorrect answer", getting immediately booted out of the server.

However, beyond this we didn't really know what to do.

*Post-CTF*: The last step we were missing was to actually get the memory address for `get_shell()`. Looking at the official writeup, it looks like we can use the `pwn` Python module with the `ELF()` object and `p64()` function to get the address of the `get_shell` function and insert it into our string, getting a shell.

This was pretty interesting, as two of us had encountered buffer overflows in some capacity from our assignments, but only in C. We remembered enough to brute force, but for the actual shell-code insertion, we were a little lost. It was also cool to see the use of `pwn` in Python - it definitely seems like a useful module.

**Flag**: DUCTF{h0w_d1d_you_c4LL_That_funCT10n?!?!?}  
**Tools**: [`pwn`](https://github.com/Gallopsled/pwntools)  
**Further reading**: [Buffer overflows in C (Aleph One)](https://inst.eecs.berkeley.edu/~cs161/fa08/papers/stack_smashing.pdf), [buffer overflows using `pwn`](https://malwaresec.github.io/Stack-Based-Buffer-Overflow/)

# Reversing

The reversing (besides the pwn) category was the hardest one of all. We were unable to get anywhere close to a solution for the ones we really did attempt, so most of this section is just "throwing stuff at the wall" and less of the "it sticks".

## Formatting
**Difficulty**: Beginner  
*Its really easy, I promise*

We're given an executable, which when run prints out:
```sh
haha its not that easy}
```

If you run `strings` on the file, you can also see a string:
```sh
DUCTF{haha its not that easy}
```

Which is unfortunately not the flag. Continuing to look at the strings, there are a bunch of lines like:

```sh
rtstuff.c 
deregister_tm_clones 
__do_global_dtors_aux 
completed.7452 
__do_global_dtors_aux_fini_array_entry 
frame_dummy 
__frame_dummy_init_array_entry 
formatting.c 
__FRAME_END__
```

Along with words like `this`, `crap`, `what`, `easy`, `heck` interspersed. 

One of the lines says

```sh
d1d_You_Just_ltrace_
```

which we saw repeated when we tried running `ltrace`, except there was some garbage behind it there. However, we stopped here and didn't look into it anymore.

*Post-CTF*: We didn't know it then, but we were literally *staring at the flag* after running `ltrace`.

**Flag**: DUCTF{d1d_You_Just_ltrace_296faa2990ac}  
**Tools**: `ltrace`  
**Further reading**: [How does `ltrace` work?](https://blog.packagecloud.io/eng/2016/03/14/how-does-ltrace-work/), [10 ways to analyse binaries](https://opensource.com/article/20/4/linux-binary-analysis)

## I'm getting Emotional
**Difficulty**: Medium  
*Bob received a very important email regarding some very important information. Can you figure out what's inside?*

*Note: the document is based on a real emotet maldoc sample! The malicious bits have been removed, but this is what real world phishing docs look like...*

The attachment is a Word(-like) file; however, opening it causes it to disappear:
* Trying to open it in Word/Notepad indicates the file has a virus and it can't be opened
* Trying to "Open and Repair" in Word also does nothing

*Post-CTF*: Apparently the key to this was [VBA](https://en.wikipedia.org/wiki/Visual_Basic_for_Applications). According to the offical writeup, all we needed to do was [set a breakpoint in the Visual Basic Editor](https://www.excel-easy.com/vba/examples/debugging.html) where the flag would be used in `strcomp()`, and then check the value.

**Flag**: DUCTF{Macr0_h4x0rman}  
**Tools**: Visual Basic Editor  
**Further reading**: [Visual Basic deobfuscation](https://blog.malwarebytes.com/cybercrime/2016/02/de-obfuscating-malicious-vbscripts/) 
## flag getter
**Difficulty**: Medium  
*An app that gets the flag for you! What more could you possibly want?*

For this challenge, we were given an APK file. I tried unzipping it (since [an APK is just a fancy ZIP file](https://www.lifewire.com/apk-file-4152929#:~:text=A%20file%20with%20the%20APK,be%20found%20on%20other%20websites.)), but it didn't seem to lead anywhere and I didn't really have the time to dig into APK analyzation.

Ruju also tried downloading it onto her phone:
!["Get the Flag" in bubbly capital letters. Below: "Response code: 200", with four coloured big buttons that read "GET flag 1/4", etc.](/img/ductf-2020/flag-getter.png)

Hitting the buttons only gives a response code "200", but nothing else. We wanted to look further into the "response code", but we ran into some installation issues with [`adb`](https://developer.android.com/studio/command-line/adb), so we were unable to actually dig into what kind of requests the app was making.

*Post-CTF*: Looking at the official write-up for this situation, I'm not actually sure we would've gotten there even if we *had* `adb` properly installed. They used [`apktool`](https://ibotpeaches.github.io/Apktool/) to decode and rebuild the APK ater patching it - we didn't even know this existed. We also had no idea what certificate pinning was or how to setup a MITM proxy.

**Flag**: DUCTF{n0t_s0_s3cre7_4ft3r_4LL_!!11!}  
**Tools** : [`apktool`](https://ibotpeaches.github.io/Apktool/), [`adb`](https://developer.android.com/studio/command-line/adb)  
**Further reading** : [Certificate pinning](https://labs.nettitude.com/tutorials/tls-certificate-pinning-101/), [`mitmproxy`](https://mitmproxy.org/)

# Conclusion
Overall, our team ended up in 162nd place (out of 1080 teams), with 1958 points! (Many of the challenges featured dynamic scoring, meaning that the number of points went down as more people solved them.) 
![Team score screenshot. same_tho with 730 points; rujube with 462; Sandamander with 766.](/img/ductf-2020/ductf-final.png)

Some other interesting facts:
* Of the flags we submitted, 2/3 were wrong (this may have had to do with my coordinate guessing for [A turn of events](#a-turn-of-events-w-some-really-bad-opsec))
* Over half of the challenges we solved were in the [misc](#misc) category
* We solved 6 "beginner" challenges, 3 "easy", both sanity-checks, and 1 "medium"

We really enjoyed the CTF, not because we felt like we learned a lot - although we definitely learned something - but mostly because it was all so *Australian*. As three people who grew up in Canada and have never traveled to Australia, it was fun seeing and learning so much about Australia (we learned what the boxing croc is! or who Clive Palmer is!).

Looking forward to Down Under CTF 2021 - maybe next year we'll be better 😊
