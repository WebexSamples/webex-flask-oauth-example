# OAuth Integration Demo

Make sure to have Python 3 installed.

To run the server application, open a command terminal, and navigate to the folder where you saved this Python script. 

Replace the required variables (Client ID, Secrect ID etc...) in the oauth.py file on lines 23 and 24, Replace the URL in the <a> tag in 
  index.html, then run:

*python3 oauth.py*

You should see this in the terminal:

Listening on http://0.0.0.0:10060...

copy and paste above URL into browser and follow prompts

**Note**: Your Redirect_URI in the Integration's settings on Developer Webex Teams site should be: http://0.0.0.0:10060/oauth or if you're using any other hosting platform it would be https://YOUR_SERVER/oauth
