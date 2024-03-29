## File Structure
```txt
.
├── app
│   ├── __init__.py
│   ├── auth
│   │   ├── __init__.py
│   │   ├── email.py
│   │   ├── forms.py
│   │   └── routes.py
│   ├── errors
│   │   ├── __init__.py
│   │   └── handlers.py
│   ├── main
│   │   ├── __init__.py
│   │   ├── forms.py
│   │   └── routes.py
│   ├── models.py
│   ├── static
│   └── templates
│       ├── auth
│       │   ├── login.html
│       │   └── register.html
│       ├── base.html
│       ├── errors
│       │   ├── 404.html
│       │   └── 500.html
│       ├── index.html
│       ├── upload_references.html
│       └── upload_signature.html
├── config.py
├── database.db
├── flasky.py
├── requirements.txt
├── static
│   └── images
│       └── user_1
│           ├── 002_02.PNG
│           ├── 002_08.PNG
│           ├── 002_09.PNG
│           ├── 002_10.PNG
│           ├── 002_11.PNG
│           ├── 07_050.png
│           ├── 08_050.png
│           └── 09_050.png
└── temp_image.jpg
```

This project verifies if a signature is real or forged. 
The structure consists of the following directories and files:
**app**: The main application package containing the core logic of the signature verification system.
**__init__.py**: Initialization file for the app package.
auth: Subpackage for authentication-related functionality.
**__init__.py**: Initialization file for the auth subpackage.
**email.py**: Module for sending email notifications.
**forms.py**: Forms definitions for user authentication.
**routes.py**: Routes and views for user authentication.
errors: Subpackage for handling error pages.
**__init__.py**: Initialization file for the errors subpackage.
**handlers.py**: Handlers for different types of errors (e.g., 404, 500).
main: Subpackage for the main functionality of the application.
**__init__.py**: Initialization file for the main subpackage.
**forms.py**: Forms definitions for main application functionality.
**routes.py**: Routes and views for the main application functionality.
**models.py**: Module defining database models and data structures.
static: Directory for static files (e.g., CSS, JavaScript).
templates: Directory for HTML templates used by the application.
auth: Subdirectory for authentication-related templates.
**login.html**: Template for the login page.
**register.html**: Template for the registration page.
**base.html**: Base template with common elements for other templates.
errors: Subdirectory for error-related templates.
**404.html**: Template for the 404 error page.
**500.html**: Template for the 500 error page.
**index.html**: Template for the main application's index page.
**upload_references.html**: Template for the upload references page.
**upload_signature.html**: Template for the upload signature page.
**config.py**: Configuration file for the application.
**database.db**: SQLite database file storing application data.
**flasky.py**: Main entry point file for running the application.
**requirements.txt**: File specifying the required Python dependencies.
static/images/user_1: Directory for storing user-specific image files.
**temp_image.jpg**: Temporary image file used in the application.
This structure separates different components of the application into logical 
modules, making it easier to organize and maintain the codebase.

## Code to be run on kaggle
```py
!pip install pyngrok
from keras.preprocessing.image import ImageDataGenerator
import os
import tensorflow as tf
import numpy as np
import subprocess
import flask
from flask import request
from pyngrok import ngrok

app = flask.Flask(__name__)

def execute_kaggle_notebook(image_path):
    # Load the saved model
    loaded_model = tf.keras.models.load_model('/kaggle/working/model_directory')

    # Preprocess the input image
    image = tf.keras.preprocessing.image.load_img(image_path, target_size=(200, 200))
    image_array = tf.keras.preprocessing.image.img_to_array(image)
    image_array = np.expand_dims(image_array, axis=0)
    image_array /= 255.0

    # Make predictions
    predictions = loaded_model.predict(image_array)
    prediction = float(predictions[0][0])

    return prediction

@app.route('/predict', methods=['POST'])
def predict():
    if 'file' not in request.files:
        return flask.jsonify({'error': 'No file part'})

    file = request.files['file']
    if file.filename == '':
        return flask.jsonify({'error': 'No selected file'})

    if file:
        image_path = 'temp_image.jpg'
        file.save(image_path)
        prediction = execute_kaggle_notebook(image_path)
        os.remove(image_path)
        return flask.jsonify({'prediction': prediction})

@app.route('/')
def index():
    return 'Welcome to the Authenticity Prediction API'

def start_ngrok():
    ngrok.set_auth_token('2K0G3tRlMdJ3Soq3AERZqj7QWJs_4McbRLKPTCi34vnHXgqU1')
    url = ngrok.connect(5000).public_url
    subprocess.run(["ngrok", "http", "5000", "--proto=http"])
    print(' * Tunnel URL:', url)

start_ngrok()
app.run()
```

### Explanation
This code sets up an API using Flask for running a signature authenticity prediction system.
Here's a breakdown of the code:

#### Import necessary packages:

- **pyngrok** for creating a public URL to expose the API
- **ImageDataGenerator** from **keras.preprocessing.image** for image preprocessing
- **os** for interacting with the operating system
- **tensorflow** for loading and running the model
- **numpy** for numerical operations
- **subprocess** for running commands in the shell
- **flask** for creating the API
- **request** from **flask** for handling HTTP requests

#### Create a Flask application instance:
```py
app = flask.Flask(__name__)
```

#### Define a function execute_kaggle_notebook(image_path):

- Loads a pre-trained model from the /kaggle/working/model_directory
- Preprocesses the input image by resizing, converting to an array, and normalizing
- Makes predictions using the loaded model
- Returns the prediction
- Define a route /predict for handling POST requests:

- Checks if a file is included in the request
- Saves the file as temp_image.jpg
- Calls execute_kaggle_notebook(image_path) to get the prediction
- Deletes the temporary image file
- Returns the prediction as JSON
- Define a route / for handling GET requests:

- Displays a welcome message
- Define a function start_ngrok():

- Sets up the ngrok authentication token
- Connects ngrok to expose the local Flask application on a public URL
- Prints the tunnel URL
- Run the start_ngrok() function to establish the ngrok tunnel.

- Start the Flask application using app.run().

Overall, this code sets up an API that accepts POST requests with an image file, performs 
signature authenticity prediction using a pre-trained model, and returns the prediction as JSON. 
The ngrok tool is used to create a public URL for accessing the API.


### How to set up and get started with ngrok API token
To set up and get started with ngrok API token, follow these steps:
1. Sign up for an ngrok account:
   - Go to the ngrok website (https://ngrok.com/) and sign up for an account by providing your email address and choosing a password.

2. Log in to your ngrok account:
   - Once you have signed up, log in to your ngrok account using your credentials.

3. Generate an API token:
   - After logging in, navigate to the ngrok dashboard.
   - In the left navigation menu, click on "Auth" to access the authentication settings.
   - Under the "Your Authtoken" section, click on the "Copy" button to copy your API token.

4. Install ngrok:
   - ngrok provides a command-line tool that needs to be installed on your machine.
   - Download the ngrok executable suitable for your operating system from the ngrok website (https://ngrok.com/download).
   - Follow the installation instructions provided for your specific operating system.

5. Authenticate ngrok with your API token:
   - Open a terminal or command prompt.
   - Run the following command, replacing <YOUR_API_TOKEN> with your ngrok API token:
     ```
     ngrok authtoken <YOUR_API_TOKEN>
     ```

6. Start a tunnel:
   - With ngrok authenticated, you can start a tunnel to expose your local server publicly.
   - In the terminal, navigate to the directory where your server is running or where the ngrok executable is located.
   - Run the following command to start the tunnel, specifying the port on which your local server is running:
     ```
     ngrok http <PORT_NUMBER>
     ```
     Replace <PORT_NUMBER> with the port number of your local server (e.g., 5000 for Flask applications).
   - ngrok will start and provide you with a public URL that forwards incoming requests to your local server.

7. Use the ngrok public URL:
   - Once the ngrok tunnel is active, you can use the provided public URL to access your local server from external devices or services.
   - Copy the ngrok URL and use it in your applications or share it with others to access your locally hosted services.

Remember to keep your ngrok API token secure and avoid sharing it with others. It 
provides access to your ngrok account and should be treated as sensitive information.

### How to set up a Kaggle account and run notebooks
To set up a Kaggle account and run notebooks, follow these steps:
1. Visit the Kaggle website:
   - Go to the Kaggle website (https://www.kaggle.com/) in your web browser.

2. Sign up for a Kaggle account:
   - Click on the "Sign Up" button on the top-right corner of the website.
   - You can sign up using your Google account, Facebook account, or create a new account using your email address.
   - Follow the prompts to complete the registration process and create your Kaggle account.
