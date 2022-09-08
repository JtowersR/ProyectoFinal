# Projecto final

En el siguiente repertorio se presentara todos los documentos para que utilizando un microcontrolador SMT32F103c8xx, se desarrolle una aplicacion que muestree un voltajes analogicos, los convierta a un valor digital y los exponga en una pantalla LCD.

---

Utilizando un microcontrolador SMT32F103c8xx, se desarrollara una aplicacion que muestree un voltajes analogicos, los convierta a un valor digital y los exponga en una pantalla LCD.

Ademas de tener como requisito lo siguiente:

• Los voltajes deben ser procesados por algun filtro digital (ejemplo: mean average filter). 

Esto quiere decir que se tomaran ciertos valores en ciertos puntos al momento de medir los voltajes 
• La pantalla LCD debe mostrar los voltajes filtrados y no filtrado

### Antes de…

---

Se utilizo el software STM32cubeIDE ya que es un programa gratuito y es de la misma empresa que fabrica los microprocesaroes STM32, esto nos permitira utilizar el maximo potencial para nuestro caso, antes de comenzar a escribir el codigo se debe iniciar por configurar el micro y dependiendo de los perifiericos a utilizar variara como se comportara finalmente el micro, despues, se continuara con la creacion del codigo el cual esta implementado en lenguaje C.

  

### Comienzo

---

El software cubeIDE es bastante completo pero tambien complejo, ya que, consta con varios programas, Eclipse es el entorno de desarrollo aplicado que integra un compliador GCC el conjunto de herramientas ademas de un compilador conocido como “Toolchain”, un generador grafico de codigo el que nos permitira iniciar todos los perifiericos y bloques del micro de manera visual, un debugger que permite ejecutar los programas por pasos, aplicar breakpoints, inspecionar variables y una herramienta de documentacion que nos dara la facilidad de acceder a toda la documentacion tales como ejemplos y comunidades de usuarios.  

Al momento de crear el projecto lo comun es que seleccionesmos el chip BluePill pero al no ser una placa oficial deberemos seleccionar en el programa el microprocesador STM32F103C8Tx LQFP48.

Otro punto importante a tener en cuenta, es seleccionar que tipo de lenguaje trabajaremos por lo cual en este caso se utilizo el C.

Ya habiendo pasado todos los pasos de creacoin del proyecto procederemos a realizar la configuracion del projecto mostrando a continuacion las opciones del programa que se expresaran a continucion.

- System Core →  SYS → debug → Serial Wire
- Analog → ADC1 → IN2 → Parameter Settings → Continuos Converion Mode → Enable.

### Filtro

---

Ahora que ya tenemos toda la base lista del proyecto prodemos realizar el requerimiento de utilizar un filtro el cual es un “Mean Average Filter” al ser del tipo sencillo nos permitira poder implementarlo sin muchas complicaciones ademas de que el mayor beneficio de utilizarlo es la efectividad que presenta al momento de eliminar el ruido, su funcionamiento se basa en remplazar cada valor de datos con promedio a un conjunto. esto elimina valores que no son representativos en el entorno.

Para implementarlo en nuestro caso lo que se realizara es tomar un dato que se transpasara entre las variables que despues se sumaran y se diviridan por la cantidad de estas para si obtener los nuevos datos promediados y filtrados del sistema.

### Conclusiones

---

En conclusiones se puede decir que… so.

### Bibliografia

---

 

- [https://www.st.com/en/development-tools/stm32cubeide.html](https://www.st.com/en/development-tools/stm32cubeide.html)
- [https://www.profetolocka.com.ar/2021/05/04/programando-la-stm32-blue-pill-con-el-cube-ide-parte-1-instalacion-y-configuracion/](https://www.profetolocka.com.ar/2021/05/04/programando-la-stm32-blue-pill-con-el-cube-ide-parte-1-instalacion-y-configuracion/)
- [https://homepages.inf.ed.ac.uk/rbf/HIPR2/mean.htm](https://homepages.inf.ed.ac.uk/rbf/HIPR2/mean.htm)

![Untitled](Projecto%20final%20be707adf6d5b4683903304dd49eeafc0/Untitled.png)