<section>In an attempt to explore Apple’s Find My Friends API, I was led to monitor the network requests coming from my iPhone 6s and actually finding some rather interesting responses.
<div>
<p id="2c82">For this guide I have used the <a href="https://www.charlesproxy.com/" target="_blank" rel="nofollow noopener noreferrer" data-href="https://www.charlesproxy.com/">Charles app</a> for Mac OSX and my iPhone 6s, although this guide and the Charles app could be applied to any other OS and hardware.</p>
<!--more-->
<h3></h3>
<h2 id="92be">Instructions</h2>
<h4 id="9fca">Step 1: Download</h4>
<p id="7548">Download a copy at <a href="https://www.charlesproxy.com/download/" target="_blank" rel="nofollow noopener noreferrer" data-href="https://www.charlesproxy.com/download/">here</a> and install on your machine. Note, this app is $50 USD (includes 4 licences) but is free on a trail for 30 days.</p>
<p id="6975">Upon first run, you will start to see SSL requests coming in their encrypted form, we will need to perform an SSL “<a href="https://en.wikipedia.org/wiki/Man-in-the-middle_attack" target="_blank" rel="nofollow noopener noreferrer" data-href="https://en.wikipedia.org/wiki/Man-in-the-middle_attack">man in the middle attack</a>” on ourselves to view the data unencrypted</p>

<figure id="d507">
<div>
<div></div>
<h6 style="text-align: center;" data-image-id="1*NFU-f5ItlZpLDLuYDZ1iVw.png" data-width="2294" data-height="1560" data-action="zoom" data-action-value="1*NFU-f5ItlZpLDLuYDZ1iVw.png" data-scroll="native"><img class="aligncenter" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/Screen-Shot-2018-10-22-at-18.24.202.png" alt="" width="1282" height="746" /><span style="text-align: center;">Encrypted Requests</span></h6>
</div></figure>
&nbsp;
<h4 id="7739">Step 2: Install Root Certificate Authority (CA) on your Mac</h4>
<p id="40e9">Go to this path:</p>
<span style="font-family: Consolas, Monaco, monospace;">[code_block_prettify]</span><span style="font-family: Consolas, Monaco, monospace;">Help &gt; SSL Proxying &gt; Save Charles Root Certificate</span><span style="font-family: Consolas, Monaco, monospace;">[/code_block_prettify]</span>

Double click to install the certificate, this should open “Keychain Access” then search “Charles Proxy CA”, right click on the certificate &gt; Get Info, open the “Trust” menu and select from the dropdown “When using this certificate” to “Always Trust”.

</div>
<p id="e40e">Go back to the Charles App and go to menu</p>
[code_block_prettify] Proxy &gt; SSL Proxy Settings... [/code_block_prettify]
<p id="8084">add host “*” and port “*” to the Locations list. Make sure to restart your browser so it now uses the new Root Certificate.</p>

<figure id="a2da">
<div>
<div></div>
<h6 style="text-align: center;" data-image-id="1*IZkttaiB3bWDYhTEqfLrWQ.png" data-width="2504" data-height="1540" data-action="zoom" data-action-value="1*IZkttaiB3bWDYhTEqfLrWQ.png" data-scroll="native"><img class="aligncenter" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/Screen-Shot-2018-10-22-at-18.58.26.png" alt="" width="1392" height="889" />Tricked!</h6>
<div data-image-id="1*IZkttaiB3bWDYhTEqfLrWQ.png" data-width="2504" data-height="1540" data-action="zoom" data-action-value="1*IZkttaiB3bWDYhTEqfLrWQ.png" data-scroll="native"></div>
</div></figure>
<p id="55b4">This will allow you to monitor SSL requests by Charles on your machine!</p>

<figure id="111b">
<div>
<div></div>
<div data-image-id="1*dbzFkI2MurDMZQ307Lv2ow.png" data-width="2294" data-height="1560" data-action="zoom" data-action-value="1*dbzFkI2MurDMZQ307Lv2ow.png" data-scroll="native"><img class="aligncenter" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/Screen-Shot-2018-10-22-at-18.26.38.png" alt="" width="1277" height="748" /></div>
<div data-image-id="1*dbzFkI2MurDMZQ307Lv2ow.png" data-width="2294" data-height="1560" data-action="zoom" data-action-value="1*dbzFkI2MurDMZQ307Lv2ow.png" data-scroll="native"></div>
</div></figure>
<h4></h4>
<h4 id="8d9c">Step 3: Setup your Phone</h4>
<p id="e8b6">You will need to send the certificate you had saved above via email to an active email account on your iOS device and be using the Apple Mail App (unfortunately, going to the recommended <a href="http://chls.pro/ssl" target="_blank" rel="nofollow noopener noreferrer" data-href="http://chls.pro/ssl">http://chls.pro/ssl</a> in Safari does not install the certificate). Click on the attached certificate in the email message and this view below should appear, install the profile:</p>

<figure id="50ed">
<div data-image-id="1*DAtkPaxzOQLb2bivxPffYw.jpeg" data-width="640" data-height="1136" data-scroll="native"><img class="aligncenter wp-image-609 size-large" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/IMG_6626-576x1024.png" alt="" width="576" height="1024" /></div></figure>
&nbsp;
<p id="1b50">Next, find out the IP of your machine (Will be in network settings) and make sure that both your iOS and Desktop OS Machine devices are connected to the same network then navigate to</p>
[code_block_prettify]
Settings App &gt; Wi-Fi &gt; WifiName Settings &gt; HTTP Proxy &gt; Manual
[/code_block_prettify]

then enter Server: <a href="https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces" target="_blank" rel="nofollow noopener noreferrer" data-href="https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces">x.x.x.x</a> (the local address of your machine found above) and Port: 8888
<figure id="2324">
<div></div>
<div data-image-id="1*932DrsyvU4GmpwG2fB9SwQ.jpeg" data-width="563" data-height="1000" data-scroll="native"><img class="aligncenter wp-image-610 size-large" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/IMG_6628-575x1024.png" alt="" width="575" height="1024" /></div>
<div data-image-id="1*932DrsyvU4GmpwG2fB9SwQ.jpeg" data-width="563" data-height="1000" data-scroll="native"></div>
<h6 style="text-align: center;" data-image-id="1*932DrsyvU4GmpwG2fB9SwQ.jpeg" data-width="563" data-height="1000" data-scroll="native">Wi-Fi Settings Page</h6>
</figure>
&nbsp;

&nbsp;
<p id="dbb0">Then go back to Charles App and click “Allow” on the popup asking for permission</p>

</section><img class="aligncenter" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/Screen-Shot-2018-10-22-at-18.47.33.png" alt="" width="891" height="273" />

&nbsp;

<section>
<div>
<div>
<p id="d386">You should now see requests coming in from your device!</p>

<figure id="2b36">
<div>
<div></div>
<div data-image-id="1*jDQHdS_vgrj8EVLws7qwGg.png" data-width="2294" data-height="1560" data-action="zoom" data-action-value="1*jDQHdS_vgrj8EVLws7qwGg.png" data-scroll="native"><img class="aligncenter" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/Screen-Shot-2018-10-22-at-18.56.13.png" alt="" width="1381" height="845" /></div>
<h6 style="text-align: center;" data-image-id="1*jDQHdS_vgrj8EVLws7qwGg.png" data-width="2294" data-height="1560" data-action="zoom" data-action-value="1*jDQHdS_vgrj8EVLws7qwGg.png" data-scroll="native">My Instagram Feed!</h6>
&nbsp;

</div></figure>
</div>
</div>
<h6 style="text-align: center;" data-image-id="1*D1RldRNtybQZpjIB8WTkuA.png" data-width="2294" data-height="1560" data-action="zoom" data-action-value="1*D1RldRNtybQZpjIB8WTkuA.png" data-scroll="native"><img class="aligncenter" src="https://oguzhanvarsak.com/wp-content/uploads/2018/10/1D1RldRNtybQZpjIB8WTkuA.png" alt="" width="1000" height="680" />Chris Southcott’s Location data on Apple’s “<a href="https://itunes.apple.com/au/app/find-my-friends/id466122094" target="_blank" rel="nofollow noopener noreferrer" data-href="https://itunes.apple.com/au/app/find-my-friends/id466122094">Find My Friends</a>”</h6>
</section><section>
<div>

<hr />

</div>
<div>
<div>
<h4 id="e2bf">Last Step: Cleanup</h4>
<p id="4065">When you are done, revert back to a legitimate CA Root by deleting the “Charles Proxy CA” certificate on the Mac in the Keychain Access App and on iOS by going to General &gt; Profile &gt; Charles Proxy and deleting the certificate.</p>

</div>
</div>
</section>
