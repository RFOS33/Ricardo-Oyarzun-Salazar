## Título: Proyecto de fin de Diplomado 
## Autor: Ricardo Oyarzun-Salazar 

__Resumen del problema a resolver (4 puntos).__
La inteligencia artificial (IA) cada vez esta abarcando más areas que ayudan al bienestar de las personas. En este contexto, las industrias acuícolas están comenzando a utilizar esta tecnología pero aún su uso es muy incipiente y no se sabe con certeza si la IA puede de manera efectiva mejorar los parámetros productivos de peces en cultivo y ser una tecnología viable en el futuro. Debido a esta problemática se ha decidido realizar un experimento utilizando IA en el fotoperiodo de la especie Salmón del Atlantico Salmo salar mantenidos en agua dulce y agua de mar con el objetivo de evaluar si la IA mejora o modifica algunos parámetros productivos de la acuicultura de Salmo salar y si existen diferencias con respecto a su fisiología en comparación al sistema tradicionl de cultivo (luces sin inteligencia artificial).  

__Resumen de los datos originales (4 puntos).__
Se tomaron datos productivos de peso, largo, factor de condición y parámetros fisiológicos de actividad enzimática de ATPAsas branquial (NKA), niveles de glucosa, calcio, cloro, albúmina y cortisol. Todas las variables mencionadas corresponden a variables respuestas, mientras que las variables predictoras o factores fueron condición (con IA y sin IA) y el factor tiempo que tiene alrededor de 18 niveles (18 fechas de muestreo) para las variables respuesta peso, largo y factor de condición, mientras que las demás variables respuestas (NKA, glucosa, calcio, cloro, albúmina, cortisol) tienen 15 niveles (15 fechas de muestreo). Todas las variables respuestas son variables cuantitativas continuas. 

_Resumen de los métodos estadísticos utilizados (4 puntos)._
Para evaluar si la IA genera un efecto en la fisiología de los peces se realizo un ANOVA de dos vías con interacción, donde las variables condición y fecha serán las variables predictoras y para el análisis se ocupo el paquete estadístico WRS2, el cual realiza analisis estadísticos robustos cuando no se pueden cumplir los supuestos de homocedasticidad y normalidad de los datos.   








