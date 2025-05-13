# 🛰️ Sistema de Monitoreo de Calidad del Aire con IoT e IA

Este proyecto simula un sistema de **monitoreo ambiental** en tiempo real, utilizando sensores IoT (simulados) y un modelo de **inteligencia artificial** para predecir la concentración de **PM2.5** (material particulado fino). El sistema incluye una **interfaz web sencilla** desarrollada con Flask para visualizar las predicciones.

---

## 🧠 ¿Qué hace este proyecto?

- Genera datos simulados de sensores ambientales: PM2.5, CO2 y NOx.
- Almacena los datos en un archivo CSV (`datos_calidad_aire.csv`).
- Entrena un modelo de regresión lineal con `scikit-learn` para predecir valores futuros de PM2.5.


---

## Generacion de .csv

![](https://github.com/Quetzal345/Prediccion-del-aire-/blob/0dde80bb4d6fc0b4f1c19953d30383d07fb06b37/csv.jpg)


## Codigo utilizado para correr la simulación
 ``` 
from flask import Flask, render_template
import pandas as pd
import random
import os
from sklearn.linear_model import LinearRegression
import joblib

app = Flask(__name__)

# Archivo de datos
archivo_csv = 'datos_calidad_aire.csv'

# Cargar datos o inicializar
if os.path.exists(archivo_csv):
    data_storage = pd.read_csv(archivo_csv)
else:
    data_storage = pd.DataFrame(columns=['pm25', 'co2', 'nox'])

# Entrenar modelo con datos históricos
def entrenar_modelo(df):
    if len(df) >= 5:  # Asegúrate de tener suficientes datos
        X = df[['co2', 'nox']]
        y = df['pm25']
        modelo = LinearRegression()
        modelo.fit(X, y)
        return modelo
    return None

# Simular nueva lectura
def simular_sensor():
    pm25 = round(random.uniform(10, 150), 2)
    co2 = round(random.uniform(400, 2000), 2)
    nox = round(random.uniform(10, 300), 2)
    return pm25, co2, nox

# Ruta principal
@app.route('/')
def index():
    global data_storage

    # Simula nueva lectura
    pm25, co2, nox = simular_sensor()

    nuevo_dato = pd.DataFrame([{'pm25': pm25, 'co2': co2, 'nox': nox}])
    data_storage = pd.concat([data_storage, nuevo_dato], ignore_index=True)

    # Entrenar modelo
    modelo = entrenar_modelo(data_storage)
    if modelo:
        entrada = pd.DataFrame([[co2, nox]], columns=['co2', 'nox'])
        prediccion = modelo.predict(entrada)[0]
    else:
        prediccion = "Modelo insuficiente"

    # Guardar datos actualizados
    data_storage.to_csv(archivo_csv, index=False)

    return render_template('index.html',
                           pm25=pm25,
                           co2=co2,
                           nox=nox,
                           prediccion=round(prediccion, 2) if isinstance(prediccion, float) else prediccion)

if __name__ == '__main__':
    app.run(debug=True)

 ```


## 📌 Futuras Mejoras

- Conectar sensores reales mediante un ESP32.
- Implementar gráficos interactivos con Chart.js o D3.js.
- Enviar notificaciones por correo o mensaje si la calidad del aire supera límites peligrosos.
- Almacenar los datos en bases de datos como Firebase o PostgreSQL.

## 👨‍💻 Autor

**Eduardo Quetzal Delgado Pimentel**  
Estudiante de Ingeniería en Computación en CUCEI, Universidad de Guadalajara

