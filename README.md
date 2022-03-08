# APLI-EXAMPLE

import json
import pprint
import requests
import csv
import sys

#INITIAL CONFIGURATION
reload(sys)
sys.setdefaultencoding('utf-8')
#
address = 'https://addm.consolidacion.tesa/api/v1.0/data/search?query= <query to get>'token = {'Authorization': 'Bearer <Token>'}
lista_datos = []



#RETURNS THE FOLLOWING ADDRESS ADDRESS AND STORES THE OUTPUT OF THAT ADDRESS ADDRESS IN THE LIST
def getNext(address):

        #Connection to the API
        try:
                response = requests.get(address,headers=token,verify=False)
        except requests.exceptions.RequestException as e:
                raise SystemExit(e)


        #Get the JSON
        datos_obtenidos = response.json()

        value = datos_obtenidos[0]
        json_string = json.dumps(value)
        datos = json.loads(json_string)

        #We introduce the result in the list
        lista_datos.append(datos)


        return datos.get("next")

contador = 0
next_url = getNext(address)

#Store the data in the list
        #
while next_url is not None:
        contador=contador+1
        print(contador)
        print(next_url)
        next_url = getNext(next_url)

print("longitud de la lista: ",len(lista_datos))

def guardaValores():

        lista_definitiva = []

        #We create the csv file
        with open('ldom.csv', 'w') as csv_file:

                for i in range(len(lista_datos)):

                        #WE EXTRACT THE DATA
                        ejemplo = lista_datos[i]


                        #WE EXTRACT HEADER AND RESULTS
                        y = ejemplo.get("results")
                        x = ejemplo.get("headings")



                        #SAVE IN A LIST THE RESULT(KEY:VALUE) OBTAINED FROM EACH MACHINE
                        for value2 in y:
                                lista_temporal = []
                                contador_cabeceras = 0
                                lista_csv = []
                                for z in value2:


                                        if (x[contador_cabeceras] == 'name'):

                                                if str(z) is not None:
                                                        name_split = z.split('.')[0]
                                                        dictionario_temporal = {x[contador_cabeceras]:name_split}
                                                        contador_cabeceras += 1
                                                        lista_temporal.append(dictionario_temporal)
                                                        lista_csv.append(name_split)
                                        else:
                                                dictionario_temporal = {x[contador_cabeceras]:z}
                                                contador_cabeceras += 1
                                                lista_temporal.append(dictionario_temporal)
                                                lista_csv.append(z)

                                csv_writer = csv.writer(csv_file, delimiter=',')
                                csv_writer.writerow(lista_csv)


                                lista_definitiva.append(lista_temporal)

        csv_file.close()

        return lista_definitiva



maquinas = guardaValores()


#We print the result
contador = 1

for b in range(len(maquinas)):
        print("************************Machine number: ",contador,"***********************")
        print(maquinas[b])

        #To show only the results
        #for valor in maquinas[b]:
        #       print(valor.values())
        contador += 1
