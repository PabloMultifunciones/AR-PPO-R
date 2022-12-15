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

Para solucionar esto aparece el PPO. Este metodo sirve para estabilizar 
