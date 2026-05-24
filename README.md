# Proyecto_integrador_matematicas
# Histórico de ventas de un restaurante para predicción de demanda mediante IA
# Integrantes
Carla Karina Rocha Hernandez

Lidia Sofia Hernandez Briseño

Maria Gabriela Salas Romo

Santiago Olivares Legaspi


# Introduccion
Desarrollamos cuatro modelos de regresión (Decision Tree, Random Forest, Gradient Boosting y XGBoost) con el objetivo de predecir las ventas de un restaurante de mariscos. La información base se extrajo del sistema Soft Restaurant 11 en formato .bak, el cual fue restaurado y analizado en SQL. Debido a la complejidad del software y su estructura de múltiples tablas relacionales, aplicamos técnicas de ingeniería de datos mediante consultas, agregaciones y uniones (JOINs) para consolidar la información. Este procesamiento nos permitió estructurar un histórico de patrones de venta esencial para estimar la demanda futura y controlar las mermas. Finalmente, para el entrenamiento de los modelos, nos enfocamos en cuatro variables principales: clave única de venta, descripción general del producto, volumen de ventas del día y fecha de registro.

# Planteamineto del problema 
El objetivo de este proyecto es reducir y, de ser posible, mitigar las pérdidas económicas y el desperdicio de mercancía en el negocio. Actualmente, la falta de control sobre la rotación de inventario genera dos problemas críticos: por un lado, los productos de baja demanda se vencen o desperdician sin generar ingresos; por otro lado, la falta de stock de los productos más vendidos provoca la pérdida de oportunidades de venta, dejando al negocio con un inventario estancado y de bajo interés para los clientes.

# Descripcion del dataset
El conjunto de datos bajo análisis está compuesto por 31,377 registros (filas) y 11 variables (columnas), consolidando un total de 345,147 observaciones. Estructuralmente, el dataset se compone de variables categóricas; tales como el identificador único del producto, su descripción general y la categoría de consumo (comida o bebida), y variables numéricas continuas que detallan el aspecto comercial, incluyendo el precio unitario, la cantidad de unidades vendidas y los ingresos totales percibidos en un día específico. Asimismo, se integraron variables de corte temporal desglosadas por día, mes, año y día de la semana. Tras una evaluación de la calidad de la información, se constató la ausencia de valores nulos o registros atípicos (outliers); por su parte, la presencia de registros con características idénticas en productos, precios o cantidades responde de forma natural al comportamiento histórico y repetitivo de las transacciones diarias del negocio.

# Desarrollo teorico
Las fornulas que ocupamos y como las utilizamos para el manejo de nuestra base de datos
# 1. Árbol de Decisión (Decision Tree Regressor)
Para decidir dónde cortar los datos, el árbol busca minimizar la Varianza o el Error Cuadrático Medio (MSE) en los grupos resultantes:
<img width="368" height="126" alt="image" src="https://github.com/user-attachments/assets/6eb013db-7425-4e11-b933-55ac880f5d7e" />

yi: El valor real de VENTA_LOG para cada fila en ese nodo.

y: El promedio de VENTA_LOG de todas las observaciones que cayeron en ese nodo (la predicción del nodo).

n: El número de muestras en ese nodo.

# 2. Random Forest Regressor
La predicción final del Random Forest es simplemente el promedio de las predicciones de todos los árboles individuales:
<img width="327" height="161" alt="image" src="https://github.com/user-attachments/assets/70ca2ab7-4f62-4a48-9f64-2f7b342647e0" />

B:El número total de árboles que decidiste crear (por ejemplo, 100 árboles).

Tb(X):La predicción individual que hace el árbol número b basándose en el valor de PRODUCTO_ENCODED (X).

# 3. Gradient Boosting Regressor
<img width="425" height="66" alt="image" src="https://github.com/user-attachments/assets/53aad9e9-5f17-4442-88ce-36f8232bf442" />

ym-1(X):La predicción acumulada hasta el árbol anterior.

hm(X):El nuevo árbol que se entrena en este paso.Este árbol no intenta predecir VENTA_LOG, intenta predecir el error r=y-ym-1

$\gamma_m$ (o $\eta$):Un número pequeño para que el modelo aprenda despacio y no se sobreajuste.

# 4. XGBoost
Para decidir cómo construir cada árbol, XGBoost busca minimizar una función que equilibra el error con la complejidad del árbol:
<img width="516" height="107" alt="image" src="https://github.com/user-attachments/assets/8ff777ee-ace7-4a14-8cf9-aabb8c6e239d" />

Donde la regularización matemática es:
<img width="350" height="120" alt="image" src="https://github.com/user-attachments/assets/710c5a53-ad45-468e-ae93-4ef04af8c1cd" />

$l(y_i, \hat{y}_i)$:La pérdida (usualmente el error cuadrático entre tu VENTA_LOG real y la predicción).

T:El número de hojas que tiene el árbol (controlado por $\gamma$ para que no crezca demasiado).

wj:Los valores o pesos asignados a las hojas del árbol (controlados por $\lambda$ para que las predicciones no sean extremadamente altas o bajas).

# Aplicacion practica del modelo 
De esta manera es que sirve cada uno de los siguientes modelos en nuestro producto si lo aplicaramos en la vida real:

Tree Regressor: Es un modelo que se basa en preguntas si o no, por lo que no es tan exacto y en este caso ignora datos como la fecha, dia, año; por lo que si un producto vende mucho, el árbol va a predecir buenas ventas sea lunes o sabado, de hecho como nos podemos dar cuenta su error es el mas alto con 616.29

Random Forest: Es un modelo mas democratico, ya que se convina con el voto de los árboles especialistas en productos y fechas, ya no es un árbol de decisión tan simple, teniendo un error mas bajo que el anterior de 555.87

Gradient Boosting: Aqui al enfocarse en arboles de secuencia, o sea, uno trabaja, el siguiente corrige y tenerlo en una profundidad de 3 limmita la variedad de productos del dataset quedando en un erro de 593.16. Sin embargo asumiendo que subimos la profundidad aunque bajaria significativamente su error, si lo comparamos con xgboos sigue siendo mejor; ya que para empezar aunque tendria mas exactitudes estas pueden ser tan pequeñas que al minimo cambio de ventas en determinado dia de otro año se descontrolarian y cairiamos en un sobreajuste, tambien este modelo es mucho mas tardado al ser uno por uno.

XGBoost: Al tener una profundidad de 10 y corregir errores en cadena el modelo encuentra un equilibrio donde da prioridad al producto, pero tomando en cuenta el año y dia; por lo que toma hasta el ultimo dato es fechas sin perder el foco central en los productos. obteniendo un margen de error mas bajo en dinero real 546.06 y obtiene un R2 más alto.

# Discusion 
Al poner a prueba todos estos modelos relacionados entre si, pero con diferencias significativas descubrimos que al final nuestro mejor modelo con un margen de error bajo de 546.06 es el XGBoost esto se debe al metodo que utiliza este modelo lo cual lo hace mas eficiente, ya que no solo se enfoca en el producto y sus ventas, se enfoca en los otros factores que tenemos en nuestra base, como la fecha, dia, mes, año y dia de la semana.  Sin embargo el modelo se limita al no tener elementos externos como el clima o eventos festivos que justifican ese error.

# Conclusiones
El presente proyecto demostró la viabilidad y el impacto de implementar modelos de aprendizaje supervisado basados en árboles de decisión para optimizar la cadena de suministro y la gestión de inventarios en un restaurante de mariscos. A través de la predicción de la demanda, se logró dar una respuesta directa a la problemática inicial: mitigar las pérdidas económicas por desabasto de productos de alta rotación y reducir el desperdicio (mermas) de artículos con baja demanda. Tambien se demostro que el XGBoost permitirá al restaurante anticipar el volumen de ventas diarias por producto. Esto se traduce en una ventaja operativa clave: planificar las compras de materia prima fresca con base en datos históricos y no en intuiciones.

# Referencias 
An Introduction to Statistical Learning. (s. f.). An Introduction To Statistical Learning. https://www.statlearning.com/
VanderPlas, J. (s. f.). Python Data Science Handbook | Python Data Science Handbook. https://jakevdp.github.io/PythonDataScienceHandbook/
