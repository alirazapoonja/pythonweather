# pythonweather
from typing import List

# List of allowed cities
ALLOWED_CITIES = ["Lahore", "Karachi", "Islamabad"]

# Custom tool behavior function
def get_weather_custom(city: str) -> str:
    if city not in ALLOWED_CITIES:
        return f"Sorry, weather data for '{city}' is not available right now."

    # Simulate fetching weather from a fake API
    fake_weather_data = {
        "Lahore": "38°C, Sunny",
        "Karachi": "34°C, Humid",
        "Islamabad": "31°C, Cloudy"
    }

    weather = fake_weather_data.get(city, "No data")
    return f"The current weather in {city} is {weather}."

# Simulate agent calling the tool
print(get_weather_custom("Lahore"))
print(get_weather_custom("New York"))

Result 
The current weather in Lahore is 38°C, Sunny.
Sorry, weather data for 'New York' is not available right now.
