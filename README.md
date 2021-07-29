## Título: Proyecto de fin de Diplomado 
## Autor: Ricardo Oyarzun-Salazar 

__Planteamiento del problema a resolver.__
La inteligencia artificial (IA) cada vez esta abarcando más areas que ayudan al bienestar de las personas. En este contexto, las industrias acuícolas están comenzando a utilizar esta tecnología pero aún su uso es muy incipiente y no se sabe con certeza si la IA puede de manera efectiva mejorar los parámetros productivos de peces en cultivo y ser una tecnología viable en el futuro en un contexto de costos y beneficios. Debido a esta problemática se ha decidido realizar un experimento utilizando IA en el fotoperiodo de la especie Salmón del Atlantico *Salmo salar* mantenidos en agua dulce y agua de mar con el objetivo de evaluar si la IA mejora o modifica algunos parámetros productivos de la acuicultura de *Salmo salar* y si existen diferencias con respecto a su fisiología en comparación al sistema tradicionl de cultivo (luces sin inteligencia artificial).

__Descripción detallada de los datos originales.__
Se tomaron datos productivos de peso, largo, factor de condición (K) y parámetros fisiológicos de actividad enzimática de ATPAsas branquial (NKA), niveles de glucosa, calcio, cloro, albúmina y cortisol en la especie *Salmo salar*. Todas las variables mencionadas corresponden a variables respuestas, mientras que las variables predictoras o factores fueron condición (con IA y sin IA) y el factor tiempo que tiene alrededor de 18 niveles (18 fechas de muestreo) para las variables respuesta peso, largo y factor de condición, mientras que las demás variables respuestas (NKA, glucosa, calcio, cloro, albúmina, cortisol) tienen 15 niveles (15 fechas de muestreo). Todas las variables respuestas son variables cuantitativas continuas. De la fecha inicial (05-10-2020) hasta la fecha 10-02-2021 los peces fueron mantenidos en agua dulce, mientras que desde las fechas 26-02-2021 hasta el 25-05-2021 los peces fueron mantenidos en agua de mar. Una flecha en las gráficas indica cuando los peces fueron trasladados al agua de mar.  

library(tidyverse)
library(readxl)
library(psych)
library(car)
library(lmtest)
library(WRS2)
library(multcomp)
library(nlme)
library(lme4)
library(readr)
library(permuco)
library(DescTools)
library(ggplot2)
install.packages("remotes")
remotes::install_github("MattCowgill/ggannotate")
library(ggannotate)

Resumen de los datos productivos

resumen_datosa <- summary(a)
pander::pander(resumen_datosa, caption = "Resumen parámetros productivos")

Resumen de la actividad enzimática NKA

resumen_datosa2 <- summary(a2)
pander::pander(resumen_datosa2, caption = "Resumen actividad enzimática NKA branquial")

Resumen de la actividad enzimática NKA

resumen_datosb <- summary(b)
pander::pander(resumen_datosb, caption = "Resumen parámetros plasmáticos")

__Análisis exploratorio de los datos__

Importación de datos productivos y ATPasa

a <- read_excel("datos_productivos_luxmeter.xlsx")
a$Fechas <- as.factor(a$Fechas)
a$Condición <- as.factor(a$Condición)
str(a)
a2 <- read_excel("nka_luxmeter.xlsx")
a2$Fechas <- as.factor(a2$Fechas)
a2$Condición <- as.factor(a2$Condición)
str(a2)

Importación de parámetros plasmáticos

b <- read_excel("parametros_plasmaticos_luxmeter.xlsx")
b$Fechas <- as.factor(b$Fechas)
b$Condicion <- as.factor(b$Condicion)
b$Cortisol <- as.numeric(b$Cortisol)
str(b)

Importación de datos para correlaciones

c <- read_excel("Datos_correlaciones_luxmeter.xlsx")
c$Fechas <- as.factor(c$Fechas)
c$Condición <- as.factor(c$Condición)
c$Glucosa <- as.numeric(c$Glucosa)
c$Calcio <- as.numeric(c$Calcio)
c$Cloro <- as.numeric(c$Cloro)
c$Albumina <- as.numeric(c$Albumina)
c$Cortisol <- as.numeric(c$Cortisol)
str(c)


Análisis exploratorio de datos

Peso

boxplot_peso <- ggplot(a, aes(x = Fechas, y = Peso, color = Condición)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Peso (gr)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = c(11.421281756838, 11.421281756838), y = c(252.79800199825, 252.79800199825), label = c("", "↓")), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_peso

geompoint_peso <- ggplot(a, aes(x = Fechas, y = Peso, color = Condición)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Peso (gr)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + geom_text(data = data.frame(x = 11.4929019465336, y = 370.423497046294, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
geompoint_peso

Largo

boxplot_largo <- ggplot(a, aes(x = Fechas, y = Largo, color = Condición)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Largo (cm)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.452862434858, y = 30.2220318334214, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_largo

geompoint_largo <- ggplot(a, aes(x = Fechas, y = Largo, color = Condición)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Largo (cm)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + geom_text(data = data.frame(x = 11.4291694615725, y = 31.0850767676125, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
geompoint_largo

Factor de condición (K)

boxplot_K <- ggplot(a, aes(x = Fechas, y = K, color = Condición)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Factor de condición (K)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.2,0.2)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.4292750046114, y = 1.38809936254499, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_K

geompoint_K <- ggplot(a, aes(x = Fechas, y = K, color = Condición)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Factor de condición (K)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.2,0.2)) + geom_text(data = data.frame(x = 11.4772042897979, y = 1.42333122295923, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
geompoint_K

NKA

boxplot_NKA <- ggplot(a2, aes(x = Fechas, y = NKA, color = Condición)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Actividad enzimática NKA (umoles ADP/mg proteina/hora)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.4772042897979, y = 16.5883528852713, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_NKA

geompoint_NKA <- ggplot(a2, aes(x = Fechas, y = NKA, color = Condición)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Actividad enzimática NKA (umoles ADP/mg proteina/hora)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + geom_text(data = data.frame(x = 11.5011689323912, y = 17.2186957365544, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
geompoint_NKA

Glucosa

boxplot_glucosa <- ggplot(b, aes(x = Fechas, y = Glucosa, color = Condicion)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = " Glucosa (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.8,0.8)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.5002483814291, y = 12.9004219851579, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_glucosa

geompoint_glucosa <- ggplot(b, aes(x = Fechas, y = Glucosa, color = Condicion)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Glucosa (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.8,0.8)) + geom_text(data = data.frame(x = 11.452862434858, y = 14.5080165430836, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
geompoint_glucosa


Calcio

boxplot_calcio <- ggplot(b, aes(x = Fechas, y = Calcio, color = Condicion)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Calcio (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + stat_summary(fun.y = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.4839693511605, y = 5.84235851318247, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_calcio

geompoint_calcio <- ggplot(b, aes(x = Fechas, y = Calcio, color = Condicion)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Calcio (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + geom_text(data = data.frame(x = 11.5074846078101, y = 6.06622963058537, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
geompoint_calcio


Cloro

boxplot_cloro <- ggplot(b, aes(x = Fechas, y = Cloro, color = Condicion)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Cloro (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.3257881705773, y = 258.718841483435, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_cloro

geompoint_cloro <- ggplot(b, aes(x = Fechas, y = Cloro, color = Condicion)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Cloro (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + geom_text(data = data.frame(x = 11.3019147740121, y = 263.0765260268, 
    label = "↓"), mapping = aes(x = x, y = y, label = label), 
    size = 10.58, inherit.aes = FALSE)
geompoint_cloro


Albúmina

boxplot_albumina <- ggplot(b, aes(x = Fechas, y = Albumina, color = Condicion)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Albumina (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.4133491471997, y = 1.51119983533728, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_albumina

geompoint_albumina <- ggplot(b, aes(x = Fechas, y = Albumina, color = Condicion)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Albumina (mmol/L)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + geom_text(data = data.frame(x = 11.4371319899446, y = 1.50921177416825, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
geompoint_albumina


Cortisol

boxplot_cortisol <- ggplot(b, aes(x = Fechas, y = Cortisol, color = Condicion)) + geom_boxplot() + labs( x = "Fechas de muestreo", y = "Cortisol (ng/mL)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + stat_summary(fun = mean, shape = 22, position = position_dodge(width = 0.75)) + geom_text(data = data.frame(x = 11.421281756838, y = 776.576160875106, label = "↓"), mapping = aes(x = x, y = y, label = label), size = 10.58, inherit.aes = FALSE)
boxplot_cortisol

geompoint_cortisol <- ggplot(b, aes(x = Fechas, y = Cortisol, color = Condicion)) + geom_point(position = position_jitterdodge(jitter.width = 0.3, dodge.width = 0.3)) + labs( x = "Fechas de muestreo", y = "Cortisol (ng/mL)") + theme(axis.text.x = element_text(angle = 20), legend.position = c(0.1,0.8)) + geom_text(data = data.frame(x = 11.3735349637076, y = 794.912470978678, 
    label = "↓"), mapping = aes(x = x, y = y, label = label), 
    size = 10.58, inherit.aes = FALSE)
geompoint_cortisol


Grafico de correlaciones

pairs.panels(c[3:11], stars = TRUE, main = "Grafica de correlaciones parámetros fisiológicos")


__Análisis estadístico de los datos__

Supuestos estadísticos

Homocedasticidad para datos productivos

leveneTest(Peso ~ Condición*Fechas, data = a, center = "median")
leveneTest(Largo ~ Condición*Fechas, data = a, center = "median")
leveneTest(K ~ Condición*Fechas, data = a, center = "median")
leveneTest(NKA ~ Condición*Fechas, data = a, center = "median")


Normalidad para datos productivos

normalidad_peso <- lm(Peso ~ Condición*Fechas, data = a)
shapiro.test(normalidad_peso$residuals)
qqPlot(normalidad_peso)
normalidad_largo <- lm(Largo ~ Condición*Fechas, data = a)
shapiro.test(normalidad_largo$residuals)
qqPlot(normalidad_largo)
normalidad_k <- lm(K ~ Condición*Fechas, data = a)
shapiro.test(normalidad_k$residuals)
qqPlot(normalidad_k)
normalidad_nka <- lm(NKA ~ Condición*Fechas, data = a)
shapiro.test(normalidad_nka$residuals)
qqPlot(normalidad_nka)


Homocedasticidad para datos plasmáticos

leveneTest(Glucosa ~ Condicion*Fechas, data = b, center = "median")
leveneTest(Calcio ~ Condicion*Fechas, data = b, center = "median")
leveneTest(Cloro ~ Condicion*Fechas, data = b, center = "median")
leveneTest(Albumina ~ Condicion*Fechas, data = b, center = "median")
leveneTest(Cortisol ~ Condicion*Fechas, data = b, center = "median")

Normalidad para datos plasmáticos

normalidad_glucosa <- lm(Glucosa ~ Condicion*Fechas, data = b)
shapiro.test(normalidad_glucosa$residuals)
qqPlot(normalidad_glucosa)
normalidad_calcio <- lm(Calcio ~ Condicion*Fechas, data = b)
shapiro.test(normalidad_calcio$residuals)
qqPlot(normalidad_calcio)
normalidad_cloro <- lm(Cloro ~ Condicion*Fechas, data = b)
shapiro.test(normalidad_cloro$residuals)
qqPlot(normalidad_cloro)
normalidad_albumina <- lm(Albumina ~ Condicion*Fechas, data = b)
shapiro.test(normalidad_albumina$residuals)
qqPlot(normalidad_albumina)
normalidad_cortisol <- lm(Cortisol ~ Condicion*Fechas, data = b)
shapiro.test(normalidad_cortisol$residuals)
qqPlot(normalidad_cortisol)

Efecto de la IA sobre el Peso

t2way(Peso ~ Condición + Fechas + Condición:Fechas, data = a,)
anovapeso <- aov(Peso ~ Condición + Fechas + Condición:Fechas, data = a)
anova(anovapeso)
TukeyHSD(anovapeso)

Efecto de la IA sobre el largo

t2way(Largo ~ Condición + Fechas + Condición:Fechas, data = a,)
anovalargo <- aov(Largo ~ Condición + Fechas + Condición:Fechas, data = a)
anova(anovalargo)
TukeyHSD(anovalargo)

Efecto de la IA sobre el factor de condición


t2way(K ~ Condición + Fechas + Condición:Fechas, data = a,)
anovak <- aov(K ~ Condición + Fechas + Condición:Fechas, data = a)
anova(anovak)
TukeyHSD(anovak)

Efecto de la IA sobre la actividad enzimática de NKA

t2way(NKA ~ Condición + Fechas + Condición:Fechas, data = a2,)
anovanka <- aov(NKA ~ Condición + Fechas + Condición:Fechas, data = a2)
anova(anovanka)
TukeyHSD(anovanka)

Efecto de la IA sobre los niveles de glucosa plasmática

t2way(Glucosa ~ Condicion + Fechas + Condicion:Fechas, data = b,)
anovaglucosa <- aov(Glucosa ~ Condicion + Fechas + Condicion:Fechas, data = b)
anova(anovaglucosa)
TukeyHSD(anovaglucosa)

Efecto de la IA sobre los niveles de calcio plasmáticos

t2way(Calcio ~ Condicion + Fechas + Condicion:Fechas, data = b,)
anovacalcio <- aov(Calcio ~ Condicion + Fechas + Condicion:Fechas, data = b)
anova(anovacalcio)
TukeyHSD(anovacalcio)

Efecto de la IA sobre los niveles de cloro plasmático

t2way(Cloro ~ Condicion + Fechas + Condicion:Fechas, data = b,)
anovacloro <- aov(Cloro ~ Condicion + Fechas + Condicion:Fechas, data = b)
anova(anovacloro)
TukeyHSD(anovacloro)

Efecto de la IA sobre los niveles de albumina plasmática

t2way(Albumina ~ Condicion + Fechas + Condicion:Fechas, data = b,)
anovaalbumina <- aov(Albumina ~ Condicion + Fechas + Condicion:Fechas, data = b)
anova(anovaalbumina)
TukeyHSD(anovaalbumina)

Efecto de la IA sobre los niveles de cortisol plasmático

t2way(Cortisol ~ Condicion + Fechas + Condicion:Fechas, data = b,)
anovacortisol <- aov(Cortisol ~ Condicion + Fechas + Condicion:Fechas, data = b)
anova(anovacortisol)
TukeyHSD(anovacortisol)

__Interpretación de resultados y conclusión__

Para evaluar si la IA genera un efecto en la fisiología de los peces se realizo un ANOVA de dos vías con interacción, donde las variables condición y fecha serán las variables predictoras y para el análisis se ocupo el paquete estadístico WRS2, el cual realiza analisis estadísticos robustos cuando no se pueden cumplir los supuestos de homocedasticidad y es robusto a la presencia de outlier. Al realizar la visualización de los dato se puede apreciar el incremento en la varianza de los datos, sugiriendo heterocedasticidad, lo cual fue confirmado con los test de levene (p < 0.05), por lo tanto realizamos anova de dos vías utilizando el paquete WRS2 y además realizamos el anova de dos vías tradicional para ir comparando ambas pruebas estadísticas. Para nuestra sorpresa el anova tradicional a pesar de existir heterocedasticidad en los datos, de todas formas fue capaz de detectar efectos significativos de los factores predictivos e incluso fue mucho más sensible en identificar diferencias significativas que utilizando el paquete WRS2. En ambos modelos se puede apreciar que la IA afecta la fisiología de los peces y este efecto es más evidente en condiciones de agua de mar que en agua dulce, ya que observa un incremento del peso y el largo en los últimos muestreos y el test a posteriori logra detectar diferencias significativas de la condición control con los peces mantenidos en la IA en los últimos 4 muestreos. Los niveles de Cortisol se reducen significativamente en los últimos muestreos, sugiriendo una reducción del estrés en los peces, además los niveles de Calcio disminuyen significativamente sugiriendo que los peces están captando el ion Calcio y utilizandolo para aumentar el crecimiento del tejido oseo. Se detectaron varias correlaciones significativas, con los niveles más altos en las variables largo y peso (0.95) lo cual es plausible, mientras que los niveles de calcio y albúmina también presentaron un alto nivel de correlación (0.68) lo cual es lógico ya que se ha descrito que la proteína albúmina moviliza ion calcio, sugiriendo procesos que afectan el proceso de crecimiento en peces. En conclusión, la IA mejora el crecimiento de los peces, especialmente en agua de mar y además modifica la fisiología de *Salmo salar*, mejorando procesos fisiológicos asociados al crecimiento, tales como disminución del cortisol e incremento de proteínas tales como albúmina. Muchos más estudios son necesarios que permitan detallar si los efectos de la IA son específicos y de corto plazo de cada salinidad o si es un efecto acumulativo donde la IA es necesaría en agua dulce para generar los efectos en agua de mar. 
