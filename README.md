# OpenWeatherMap-API
Requests to the API can take a few moments to complete. If we perform these in the main application loop this will cause our app to hang while waiting for data. To avoid this we perform all requests in seperate worker threads,

This worker collects both the current weather and a forecast, and returns this to the main thread to update the UI.

First we define a number of custom signals which the worker can emit. These include finished a generic signal for the worker completing, error which emits an Exception message should an error occur and result which returns the result of the API call. The data is returned as two separate dict objects, one representing the current weather and one for the forecast.
The WeatherWorker runnable handles the actual requests to the API. It is initialized with a single parameter location which gives the location that the worker will retrieve the weather data for. Each worker performs two requests, one for the weather, and one for the forecast, receiving a JSON strings from the OpenWeatherMap.org. These are then unpacked into dict objects and emitted using the .result signal.The Raindar UI was created using Qt Designer, and saved as .ui file, which is available for download. This was converted to an importable Python file using pyuic5.

With the main window layout defined in Qt Designer. To create the mainwindow we simply create a subclass of Ui_MainWindow (and QMainWindow) and call self.setupUi(self) as normal.

To trigger the request for weather data using the push button we connect it's pressed signal to our custom update_weather slot.

Finally we create our thread pool class, to handle running our workers and show the main window.
Pressing the button triggers the update_weather slot method. This creates a new WeatherWorker instance, passing in the currently set location from the lineEdit box. The result and error signals of the worker are connected up to the weather_result handler, and to our custom alert handler respectively.

The alert handler uses QMessageBox to display a message box window, containing the error from the worker.
The weather and forecast dict objects returned by the workers are emitted through the result signal. This signal is connected to our custom slot weather_result, which receives the two dict objects. This method is responsible for updating the UI with the result returned, showing both the numeric data and updating the weather icons.

The weather results are updated to the UI by setText on the defined QLabels, formatted to decimal places where appropriate.The timestamps are processed using a custom from_ts_to_time_of_day function to return a user-friendlier time of day in am/pm format with no leading zero.
The OpenWeatherMap.org has a custom mapping for icons, with each weather state indicated by a specific number — the full mapping is available here. We're using the free fugue icon set, which has a pretty complete set of weather-related icons. To simplify the mapping between the OpenWeatherMap.org and the icon set, the icons have been renamed to their respective OpenWeatherMap.org numeric code.
First we set the current weather icon, from the weather dict, then iterate over the first 5 of the provided forecasts. The forecast icons, times and temperature labels were defined in Qt Designer with the names forecastIcon<n>, forecastTime<n> and forecastTemp<n>, making it simple to iterate over them in turn and retrieve them using getattr with the current iteration index.
  A few simple ways you could extend this application —

Eliminate repeated requests for the data, by using request_cache. This will persist the request data between runs.
Support for multiple locations.
Configurable forecast length.
Make the current weather available on a toolbar icon while running.
