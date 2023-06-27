# Flask JWT Extended Demo
Sample project to demonstrate jwt based session management in python using flask jwt extended package.

![screenshot](demo/demo.gif)

## Quickstart

- Create a Python virtual environment:

```
python -m venv .venv
```

- Activate the virtual environment:

```
source .venv/bin/activate  # for ubuntu
.\venv\Scripts\activate    # for windows
```

- Install the dependencies using `pip`

```
pip install -r requirements.txt
```
or
```
pip install flask-jwt-extended
```

- Run the app:

```
python app.py
```

## Source code

```py
from flask import Flask, request, jsonify
from flask_jwt_extended import (
    JWTManager, jwt_required, create_access_token,
    get_jwt_identity, unset_jwt_cookies
)
import hmac

app = Flask(__name__)
app.config['JWT_SECRET_KEY'] = 'your-secret-key'
jwt = JWTManager(app)

@app.route('/login', methods=['POST'])
def login():
    username = request.json.get('username')
    password = request.json.get('password')

    # Add your authentication logic here
    # Validate the username and password

    if username == 'admin' and password == 'password':
        # Generate the access token
        access_token = create_access_token(identity=username)
        response = jsonify({'login': True})
        response.set_cookie('access_token', access_token)
        print(access_token)
        return response

    return jsonify({'login': False}), 401

@app.route('/protected', methods=['GET'])
@jwt_required()
def protected():
    current_user = get_jwt_identity()
    return jsonify(logged_in_as=current_user), 200

@app.route('/logout', methods=['POST'])
@jwt_required()
def logout():
    response = jsonify({'logout': True})
    unset_jwt_cookies(response)
    return response

if __name__ == '__main__':
    app.run(debug=True)
```