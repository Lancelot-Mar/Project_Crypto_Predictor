```python
!pip install pycoingecko
```

```python
import pandas as pd
from pycoingecko import CoinGeckoAPI
from sklearn.linear_model import LinearRegression

# Creamos una instancia de la API de CoinGecko
cg = CoinGeckoAPI()

# Obtenemos los datos de bitcoin en tiempo real desde la API de CoinGecko
bitcoin_data = cg.get_coin_market_chart_by_id(id='bitcoin', vs_currency='eur', days=365)

# Creamos un dataframe con los datos de bitcoin
df = pd.DataFrame(bitcoin_data['prices'], columns=['timestamp', 'price'])

# Convertimos la columna de timestamps a datetime y la establecemos como índice del dataframe
df['timestamp'] = pd.to_datetime(df['timestamp'], unit='ms')
df.set_index('timestamp', inplace=True)

# Agregamos una columna con el cambio de precio diario
df['daily_change'] = df['price'].pct_change()

# Eliminamos los registros con valores nulos o infinitos
df.dropna(inplace=True)

# Separamos los datos en variables X (días anteriores) e y (cambio de precio para el día actual)
X = df[['price', 'daily_change']].iloc[:-1]
y = df['daily_change'].iloc[1:]

# Entrenamos un modelo de regresión lineal utilizando los datos de X e y
reg = LinearRegression().fit(X, y)

# Obtenemos los datos del último día disponible
ultimo_dia = df.iloc[-1]

# Creamos un nuevo dataframe con los datos del último día disponible
nuevo_dia = pd.DataFrame(ultimo_dia[['price', 'daily_change']]).T

# Realizamos una predicción del cambio de precio para el nuevo día utilizando el modelo entrenado
cambio_predicho = reg.predict(nuevo_dia)

# Calculamos el precio predicho para el nuevo día
precio_predicho = ultimo_dia['price'] + cambio_predicho[0]

# Imprimimos el precio predicho para el nuevo día
print("El precio predicho para el nuevo día es: ", precio_predicho)
```

Resultado = 

El precio predicho para el nuevo día es:  38534.77664218706
