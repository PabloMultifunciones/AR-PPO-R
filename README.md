# AR-PPO-R
Aprendizaje Reforzado - Proximal Policy Optimization - Resumen

### El motivo
Este algoritmo fue creado con el fin de que durante la etapa de entrenamiento se evitaran pasos de actualizacion (Gradient Steps) muy largos que probocaran daños en el aprendizaje del agente.

### ¿Como se hace esto?
Para hacer esto se calcula el ratio entre la politica nueva y la politica vieja y luego se clipea en un ratio que va a ser entre [0.8, 1.2].

#### ¿Que es clipear?
Dado un intervalor de limites y un numero o arreglo con numeros, el clipeo hace que todos los valores se mantengan entre dichos limites. Por ejemplo si tenemos un arreglo con numeros y dos limites [0, 1], todos aquellos valores que sean mayores a 1, se combertiran en 1 y todos quellos valores que sean menores a 0 se combertiran en 0. En cambio todos aquellos valores que esten entre estos dos limites permaneceran igual.

### El problema de la funcion de perdida
Esta es la funcion de perdida de la politica:  
![lxdvqu1lno5xulbujb9l](https://user-images.githubusercontent.com/95035101/207947774-2008046d-2544-4f22-9a1a-ce9d815411ca.jpg)

L(Q) - Perdida Politica  
E - Esperado  
logπ... - Logaritmo probabilidad de tomar esa accion en ese estado   
A - Ventaja   

La idea de esta funcion es hacer el Ascenso del Gradiente para que la funcion obtenga mayores valores. De esta manera nuestro agente es obligado a tomar mejores decisiones que le den mayores recompensas.  

El problema aparece en el tamaño de los pasos:  
* Si son pasos muy pequeños, la red neuronal tardara mas tiempo en converger a la solucion
* Si son pasos muy grandes, la red neuronal nunca podra converger a la solucion completamente

Para solucionar esto aparece el PPO. Este metodo sirve para estabilizar del actor durante el entrenamiento limitando cada actualizacion.

Para hacer esto se introduce una nueva funcion llamada "Funcion Objetivo Sustituto Recortado" que como el nombre indica va a restringir los cambios de politicas sustituyendo aquellos valores que sobrepasen los limites. 

### Funcion Objetivo Sustituto Recortado

En vez de utilizar el logaritmo de la probabilidad de la accion en cierto estado, se utiliza el ratio de la probabilidad de la accion con la politica actual dividido la probabilidad de accion con la politica vieja.

![rrr](https://user-images.githubusercontent.com/95035101/207971692-f3f34111-742b-4343-acff-a52add2cbaf0.png)

rt(Q) es el ratio de probabilidad entre la nueva y la vieja politica: 

* Si rt(Q) > 1, la accion es mas probable en la politica actual que en la vieja.  
* Si 0 < rt(Q) < 1, la accion es menos probable en la politica actual que en la vieja.  

Sin embargo, si el ratio es mas probable en la politica actual que en la vieja, se podria llegar a una actualizacion excesiva de la politica.  

Necesitamos restringir la funcion objetivo penalizando los cambios que esten fuera de un ratio (En el Paper oficial solo puede variar entre 0.8 y 1.2). Para hacer esto tenemos que aplicar la "Funcion Objetivo Sustituto Recortado". Esto nos asegurara que no tendremos cambios muy grandes en las actualizaciones de la politica.

![download](https://user-images.githubusercontent.com/95035101/207981983-a7775b35-fe9e-484f-abfb-b7a77b22d6e0.svg)

Con la "Funcion Objetivo Sustituto Recortado" tenemos dos ratios de probabilidad, el ratio de valores que estan fuera de los limites y el ratio de valores que esta dentro de los limites (Entre [1−∈,1+∈]) siendo ∈ un hiperparametro que por lo general es 0.2.

![rrrzz](https://user-images.githubusercontent.com/95035101/208219664-18c6ea4a-d8f1-41a7-8a66-dd56694ee211.png)  

Esta funcion toma el valor minimo de la funcion objetivo y de la funcion objetivo recortada. Esto nos ayuda a que NO nos volvamos muy codiciosos y hagamos actualizaciones que esten fuera del radio de confianza.

![ffff](https://user-images.githubusercontent.com/95035101/208267791-094e173e-ff6a-4fd3-8028-ac159cb8003e.png)

El componente A es un estimado de la ventaja que hay en un determinado estado S al tomar una determinada Accion. Si A > 0 quiere decir que la accion tomada en ese momento fue mejor que el promedio de las acciones tomadas en ese estado. Esto es asi porque la ventaja A se obtiene como la sustraccion entre la recompensa para una accion en un estado y la recompensa estimada por el critico, por lo tanto si la ventaja es positiva significara que la accion que se tomo es mejor de lo que se esperaba.  

Pero... ¿Cual es la logica detras de esos dos graficos? La logica es la siguiente:
* A > 0: En este caso la accion actual fue buena y en caso de que llegase a ser mas probable en la politica actual que en la anterior, solo podra actualizarse hasta el limite 1+∈, lo cual significa que la accion NO podra ser 1000 veces mas probable que en la politica anterior. Por otro lado, si la accion en la politica actual es menor que en la politica vieja, puede actualizarse tanto como quiera, es decir, puede deshacer el camino que hizo porque sabemos que por donde vino es un camino seguro.
* A < 0: En este caso la accion actual fue mala y en caso de que llegase a ser menos probable en la politica actual que en la anterior, solo podra actualizarse hasta el limite 1-∈, para evitar que la actualizacion sea muy grande y dañemos el proceso de entrenamiento. Por otro lado, si la accion en la politica actual es mayor que en la politica vieja, puede actualizarse tanto como quiera, es decir, puede deshacer el camino que hizo porque sabemos que por donde vino es un camino seguro.  

Resumidamente gracias a la Funcion Objetivo Sustituto Recortada el rango en el que variara la politica actual con respecto a la politica vieja sera restringido porque el incentivo para moverse fuera de dicho ratio es removido. Si el ratio es > 1+e o < 1-e el gradiente va a ser igual a 0, por lo tanto ambas regiones no nos permitiran ser muy codiciosos.
