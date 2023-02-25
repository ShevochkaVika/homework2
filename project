
import datetime as dt
import json
import requests
from flask import Flask, jsonify, request

API_TOKEN = ""
API_KEY = ""

app = Flask(__name__)


def gen_weather(location, day):
    url_base_url = "https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline"

    payload = {}
    headers = {"Authorization": API_KEY}
    
    url=f"{url_base_url}/{location}/{day}?key={API_KEY}"

    response = requests.request("GET", url, headers=headers, data=payload)
    return json.loads(response.text)



class InvalidUsage(Exception):
    status_code = 400

    def __init__(self, message, status_code=None, payload=None):
        Exception.__init__(self)
        self.message = message
        if status_code is not None:
            self.status_code = status_code
        self.payload = payload

    def to_dict(self):
        rv = dict(self.payload or ())
        rv["message"] = self.message
        return rv


@app.errorhandler(InvalidUsage)
def handle_invalid_usage(error):
    response = jsonify(error.to_dict())
    response.status_code = error.status_code
    return response


@app.route("/")
def home_page():
    return "<h2>Homework2</h2>"


@app.route(
        "/content/api/v1/integration/generate", 
        methods=["POST"])


def weather_endpoint():

    json_data = request.get_json()

    if json_data.get("token") is None:
        raise InvalidUsage("token is required", status_code=400)

    token = json_data.get("token")

    if token != API_TOKEN:
        raise InvalidUsage("wrong API token", status_code=403)

    requester_name = ""
    location = ""
    date = ""

    if json_data.get("requester_name"):
        requester_name = json_data.get("requester_name")   
        
    if json_data.get("location"):
        location = json_data.get("location")
        
    if json_data.get("date"):
        date = json_data.get("date")

    weather = gen_weather(location, date)

    result = {
        "token": token,
        "requester_name": requester_name,
        "location": location,
        "date": date,
        "weather": weather
       }

    return result
