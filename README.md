import streamlit as st
import requests
from datetime import datetime

# Set up your API key
API_KEY = "your_api_key_here"
BASE_URL = "https://api.openweathermap.org/data/2.5/weather"
FORECAST_URL = "https://api.openweathermap.org/data/2.5/forecast"

# Helper functions
def get_weather(city):
    params = {"q": city, "appid": API_KEY, "units": "metric"}
    res = requests.get(BASE_URL, params=params)
    return res.json()

def get_forecast(city):
    params = {"q": city, "appid": API_KEY, "units": "metric"}
    res = requests.get(FORECAST_URL, params=params)
    return res.json()

# Streamlit app UI
st.title("🌦 Real-Time Weather App")
city = st.text_input("Enter City Name")

if city:
    weather = get_weather(city)

    if weather.get("cod") != 200:
        st.error("City not found!")
    else:
        st.subheader(f"Current Weather in {city.title()}")
        st.write(f"**Temperature:** {weather['main']['temp']}°C")
        st.write(f"**Condition:** {weather['weather'][0]['description'].title()}")
        st.write(f"**Humidity:** {weather['main']['humidity']}%")
        st.write(f"**Wind:** {weather['wind']['speed']} m/s")

        # Forecast
        forecast_data = get_forecast(city)
        st.subheader("5-Day Forecast (Every 3 hours)")
        for item in forecast_data['list'][:10]:  # Limit to first 10 entries
            time = datetime.fromtimestamp(item['dt']).strftime("%Y-%m-%d %H:%M")
            temp = item['main']['temp']
            desc = item['weather'][0]['description']
            st.write(f"{time} | {temp}°C | {desc}")
# weather_app
