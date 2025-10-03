# Casi-que-no-profe
import requests
import pandas as pd
import matplotlib.pyplot as plt
url = 'https://raw.githubusercontent.com/Wiky-25/Laelegida/refs/heads/main/Datos_dict.json' #link datos relevantes Banco de la República
headers = {'Accept':'application/vnd.github.v3+json'}
r = requests.get(url, headers = headers)
print(r.status_code) # Imprime el status
if r.status_code == 200:
  print('Acceso exitoso\n')              # Verificación correcta a los datos
  response_dict = r.json()
else:
  print(f'No se pudo acceder a los datos, código de error: {r.status_code}')
  response_dict
  print(f'Las claves del diccionario response_dict son:')     # Claves = años
for key in response_dict.keys():
  print(key)
repo_dicts = response_dict['31/12/2004']
print(f'Número de indicadores por año: {len(repo_dicts)}')
type(repo_dicts)
for key in sorted(repo_dicts):       # Indicador por un año
  print(key)
  i = 1
for repo in response_dict.values():
  print(f'Fecha: {repo['Fecha']}')
  print(f'Inflación SAR %: {repo['Inflación sin alimentos ni regulados, anual(Dato fin de año)']}')
  print(f'Inflación total %: {repo['Inflación total, anual(Dato fin de año)']}')
  print(f'Variación salario mínimo %: {repo['Salario mínimo mensual, variación anual(Dato fin de año)']}')
  print(f'TRM: {repo['Tasa Representativa del Mercado (TRM)(Dato fin de año)']}')
  print(f'Tasa de política monetaria: {repo['Tasa de política monetaria(Dato fin de año)']}\n')
i += 1                                                               # Indicadores y valores para cada año
class IndicadoresColombia:
    """Clase para graficar indicadores económicos de Colombia"""

    def __init__(self, datos):
        """Método de inicialización"""
        self.datos = datos
        self.fechas = []
        self.trm = []
        self.tpm = []
        self.inflacion_total = []
        self.inflacion_sin_alimentos = []
        self.salario_minimo = []
        self.procesar_datos()

    def str_to_float(self, valor):
        """Convierte strings a float manejando comas y puntos"""
        return float(valor.replace('.', '').replace(',', '.'))

    def procesar_datos(self):
        """Extrae, limpia y convierte los datos"""
        for key in self.datos:
            self.fechas.append(pd.to_datetime(key, dayfirst=True))
            self.trm.append(self.str_to_float(self.datos[key].get('Tasa Representativa del Mercado (TRM)(Dato fin de año)')))
            self.tpm.append(self.str_to_float(self.datos[key].get('Tasa de política monetaria(Dato fin de año)')))
            self.inflacion_total.append(self.str_to_float(self.datos[key].get('Inflación total, anual(Dato fin de año)')))
            self.inflacion_sin_alimentos.append(self.str_to_float(self.datos[key].get('Inflación sin alimentos ni regulados, anual(Dato fin de año)')))
            self.salario_minimo.append(self.str_to_float(self.datos[key].get('Salario mínimo mensual, variación anual(Dato fin de año)', None)))

    def graficar(self):
        """Gráfico interactivo"""
        traces = [
            go.Scatter(x=self.fechas, y=self.trm, mode='lines+markers', name='TRM', line=dict(color='royalblue', width=3), yaxis='y1'),
            go.Scatter(x=self.fechas, y=self.tpm, mode='lines+markers', name='TPM', line=dict(color='green', width=3), yaxis='y2'),
            go.Scatter(x=self.fechas, y=self.inflacion_total, mode='lines+markers', name='Inflación Total', line=dict(color='firebrick', width=3), yaxis='y2'),
            go.Scatter(x=self.fechas, y=self.inflacion_sin_alimentos, mode='lines+markers', name='Inflación sin Alimentos', line=dict(color='orange', width=3), yaxis='y2'),
            go.Scatter(x=self.fechas, y=self.salario_minimo, mode='lines+markers', name='Salario Mínimo', line=dict(color='purple', width=3), yaxis='y2')
        ]   # Número de trazos en la gráfica

        layout = go.Layout(
            title='Indicadores económicos de Colombia (2004-2023)',
            xaxis=dict(title='Fecha', tickformat='%Y'),
            yaxis=dict(title='TRM (Pesos)', side='left', showgrid=True, gridcolor='lightgray'),
            yaxis2=dict(title='Porcentajes (%)', side='right', overlaying='y', showgrid=False),
            plot_bgcolor='whitesmoke',
            hovermode='x unified',
            legend=dict(title='Indicadores', bgcolor='rgba(255,255,255,0.8)', bordercolor='black', borderwidth=1)
        )

        fig = go.Figure(data=traces, layout=layout)
        py.iplot(fig)
mi_grafico = IndicadoresColombia(response_dict)
mi_grafico.graficar()
