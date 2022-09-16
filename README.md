# jesus-limon-fastapi
jesus limon DSH FastApi


from fastapi import FastAPI
from pydantic import BaseModel
#from fastapi import FastAPI, File, UploadFile
import pandas as pd
import numpy as np
import dateutil
import pytz



app = FastAPI()

@app.get("/max_anio")
def max_anio():
    df5 = pd.read_csv('races.csv')
    df5.head()
    df5= df5.sort_values("round",ascending=False, inplace=False)
    maximo_anio  = df5['year'].iloc[0]
    return{"Año con más carreras": "{maximo_anio}"}

@app.get("/piloto/max/primero")
async def primero():
        df2 = pd.read_csv('results.csv')
        df2.drop('Unnamed: 0', inplace=True, axis=1)
        df2.replace('\\N','na', inplace = True)
        df2['position'] = df2['position'].replace('na','0')
        df2["position"] = df2["position"].astype(str).astype(int)
        campeon = df2.loc[(df2['position']==1)]
        campeon.groupby('driverId')["position"].count()
        #buscamos la primera el driver id del conductor 1 en la tabla driver
        pdObj2 = pd.read_json("drivers.json", lines = True )
        driver = pd.read_csv('drivers.csv')
        driver.drop('Unnamed: 0', inplace=True, axis=1)
        driver=driver.replace("{'forename': '", "")
        driver=driver.replace("', 'surname': '"," ")
        driver=driver.replace("'}", "")
        nombre=driver.loc[(driver['driverId']==1)]
        drivername  = nombre['name'].iloc[0]
        return{"piloto max primeros lugares": "{drivername"}

@app.get("/circuito/max/carrera")
async def circuito():
    df5 = pd.read_csv('races.csv')
    recorrido = df5.groupby('circuitId')["circuitId"].count()
    recorrido.sort_values(ascending="False")
    autodromo= df5.loc[(df5['circuitId']==14)]
    autodromo1  = autodromo['name'].iloc[0]
    return{"circuito max carreras": "{autodromo1}"}



@app.get("/piloto/max/puntos/constructor/")
async def constructor():
    pdObj = pd.read_json("constructors.json", lines = True )
    pdObj.to_csv('onstructors.csv', index=True)
    df1 = pd.read_csv('constructors.csv')
    df1.drop('Unnamed: 0', inplace=True, axis=1)
    df2 = pd.read_csv('results.csv')
    df20 =df2.copy()
    df21=df20.groupby(['driverId', 'constructorId'])["points"].sum()
    df21.sort_values(ascending=False)
    constructor_name = df1['name'].iloc[0]
    driver = pd.read_csv('drivers.csv')
    driver.drop('Unnamed: 0', inplace=True, axis=1)
    driver=driver.replace("{'forename': '", "")
    driver=driver.replace("', 'surname': '"," ")
    driver=driver.replace("'}", "")
    nom_combinado= driver.loc[(driver['driverId']==18)]
    nom_combinado1 = nom_combinado['name'].iloc[0]
    return{"piloto max puntos y constructor britanico o americano": "{constructor_name} {nom_combinado1}"}
