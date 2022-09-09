# Proyecto final

En el siguiente repertorio se presentara todos los documentos para que utilizando un microcontrolador SMT32F103c8xx, se desarrolle una aplicacion que muestree un voltaje analogico, los convierta a un valor digital y los exponga en una pantalla LCD.

---

Debe cumplir con los siguientes requisitos:

• El voltaje debe ser procesado por algun filtro digital (ejemplo: mean average filter). 

Esto quiere decir que se tomaran ciertos valores en ciertos puntos al momento de medir los voltajes 
• La pantalla LCD debe mostrar los voltajes filtrados y no filtrado

### Antes de…

---

Se utilizo el software STM32cubeIDE ya que es un programa gratuito y es de la misma empresa que fabrica los microprocesaroes STM32, implementado en la bluepill, esto nos permitira obtener el maximo potencial para nuestro proyecto, pero antes de comenzar a escribir el codigo se debe empezar por configurar el micro y dependiendo de los perifiericos a utilizar variara como se comportara finalmente el micro, despues, se procedera con la creacion del codigo el cual ademas sera trabajado en lenguaje C.

![Untitled](Proyecto%20final%20a0219db511114ebabe4206b847e8894a/Untitled.png)

El software cubeIDE es bastante completo y tambien complejo, ya que, consta con varios programas tales como; Eclipse, es el entorno de desarrollo aplicado que integra un compliador GCC un gran conjunto de herramientas codificadoras ademas de un compilador conocido como “Toolchain”, un generador grafico de codigo el que nos permitira iniciar todos los perifiericos y bloques del micro de manera visual, un debugger que permite ejecutar los programas por pasos, aplicar breakpoints, inspecionar variables y una herramienta de documentacion que nos dara la facilidad de acceder a toda la documentacion tales como ejemplos y comunidades de usuarios.  

Al momento de crear el proyecto lo comun es que seleccionesmos el chip BluePill pero al no ser una placa oficial deberemos seleccionar en el programa el microprocesador STM32F103C8Tx LQFP48.

Filtro

---

El filtro que implementaremos lleva por nombre “Mean Average Filter” y entra en la categoria de los mas simple lo que nos  permitira poder implementarlo sin muchas complicaciones ademas de que el mayor beneficio de utilizarlo es la efectividad que presenta al momento de eliminar el ruido, su funcionamiento en simples palabras se basa en remplazar cada valor de datos con promedio a un conjunto. esto elimina valores que no son representativos en el entorno, como se puede ver en la imagen de referencia.

![Untitled](Proyecto%20final%20a0219db511114ebabe4206b847e8894a/Untitled%201.png)

Para implementarlo en nuestro caso lo que se realizara es tomar un dato que se transpasara entre las variables que despues se sumaran y se diviridan por la cantidad de estas para si obtener los nuevos datos promediados y filtrados del sistema.

Pantalla

---

La pantalla a utilizar corresponde a una LCD alfanumerica de 16 columnas con 2 filas que sera conectada al modulo de interface I2C, una caracteristica importante de esta pantalla es que al necesitar una poca cantidad de voltaje su brillo y niveles de ajuste permiten una mejor visualicacion de los datos a comparacion de otras pantallas mas grandes.

![Untitled](Proyecto%20final%20a0219db511114ebabe4206b847e8894a/Untitled%202.png)

### Comienzo.

---

Ya habiendo pasado todos los pasos de creacion del proyecto procederemos a realizar la configuracion mostrando a continuacion las opciones del programa que son necesarias de modificar para 

- System Core →  SYS → debug → Serial Wire.
- Analog → ADC1 → IN2 → Parameter Settings → Continuos Converion Mode → Enable.
- Connectivity → I2C1→ Mode: I2C →Parameter Settings → I2C Clock speed (Hz): 150000.

Ya realizando esas configuraciones podremos seguir haciendo clic donde la herramienta que creara el codigo que en este software tiene forma de engranaje, es necesario recalcar que si se realizan cambios en la configuracion de software agregando o quitando perifericos sera crucial volver a generar el codigo, ya que, se presentaran fallas de inconsistencia entre codigo y software.

Como paso extra es necesario implementar en las carpetas creadas  dos codigos .c y .h para poder tener toda la libreria que nos permitira tener la comunicacion necesaria entre el microprocesador y el I2C. Para nuestro caso estas se encuentran dentro de la carpeta:

- core → Inc → I2c-lcd.h
- core → Scr → I2c-lcd.c

Ya ubicados en la interface denominada Main.c prodremos comenzar a escribir el codigo

lo primero sera definir las variables principales en el espacio que el hardware creo siendo este debajo de la seccion “int main void”. 

| uint16_t raw_data;
uint16_t volt[4];
uint16_t volt2[4]; |
| --- |

raw_data corresponde con nuestro dato de entrada que se recibira.

Volt es el dato de voltaje medido primero.

volt2 es el segundo voltaje medido pero ya filtrado.

A continuacion de debe escribir la linea de codigo que despertara al ADC. utilizando el segundo espacion creado por hardware. 

| HAL_ADC_start (&hadc1);                                                  //ADC start  |
| --- |

Ademas dentro de este mismo espacio sera utilizado para definir las variables utilzadas para el filtro:

| float V1;                                                                   
float A1;
int A2=0, A3=0, A4=0;
int N=4;
float filtro; |
| --- |

Seguido podremos escribir el inicio del ADC y el filtro en la seccion “While” del codigo.

|                                                 //inicio ADC                                                                                                                 HAL_ADC_PollForConversion(&hadc1, HAL_MAX_DELAY);
raw_data = HAL_ADC_GetValue(&hadc1);
                                                //fin adc
                                                //valor voltaje sin filtrar
V1= (raw_data*3.3)/4011;
                                               //inicio Mean average filter
A4=A3;
A3=A2;
A2=A1;
A1=V1;
filtro= (A1+A2+A3+A4)/N;
                                               //fin filtro
                                               //Envio de informacion a LCD
lcd_clear();	                       //Limpiar la pantalla
HAL_Delay(1500);
lcd_init ();	                               //INITIALIZE THE LCD (** if the LCD is cleared, you need to initialize it again ***)
lcd_put_cur(0,0);
HAL_Delay(10);
lcd_send_string ("Volts [V]");  //se presenta en la pantalla LCD la palabra voltaje
                                               //Parte no filtrada LCD
gcvt(V1, 4, volt);                     //v1:voltaje adquirido, 4:largo, volt:transformacion a palabra
lcd_put_cur(1,2);
lcd_send_string (volt);
                                              //Parte filtrada LCD
gcvt(filtro, 4, volt2);	              //v1:voltaje adquirido, 4:largo, volt2:transformacion a palabra
lcd_put_cur(1,11);
lcd_send_string (volt2);
HAL_Delay(1000);
                                              //Fin envio de informacion |
| --- |

Ahora se podria utilizar el modo debbug, ubicado en la parte superior con el icono verde en forma de bichito, esto nos servira para asegurarnos de que el codigo no tenga errores grabes ademas de poder ver mas detalladamente como los datos de las variables van cambiando y  si todo se encuentra dentro de los resultados esperados.

De esta manera se concluye con los trabajos en codigo ahora procederemos a realizar el conexionado del los diferentes componentes los cuales corresponden a:

- Potenciometro
- Microprocesador
- Usb
- I2C
- Pantalla LCD
- Cables

Y tiene que quedar como se presentara a continuacion.

![d5dda725-e103-4a1c-98dd-77208ec4bc65.png](Proyecto%20final%20a0219db511114ebabe4206b847e8894a/d5dda725-e103-4a1c-98dd-77208ec4bc65.png)

Una vez realizada la conexion de los componentes podremos correr el codigo.

Cuando el codigo se termina de enviar los datos representado en la pantalla corresoponde con lo esperado, ya que, en la primera fila se muestra la palabra volts [V] y en la segunda fila se encuentran los dos voltajes, como se presentara en la siguiente imagen.

![WhatsApp Image 2022-09-08 at 3.09.09 PM.jpeg](Proyecto%20final%20a0219db511114ebabe4206b847e8894a/WhatsApp_Image_2022-09-08_at_3.09.09_PM.jpeg)

En la imagen anterior se puede ver la diferencia entre el primer voltaje que corresponde al original y el segundo que es el voltaje filtrado. 

Ademas para corroborar que las mediciones estan siendo realizadas de manera correcta se utilizo un multimetro como se presenta en la siguiente imagen.

![WhatsApp Image 2022-09-08 at 3.09.06 PM.jpeg](Proyecto%20final%20a0219db511114ebabe4206b847e8894a/WhatsApp_Image_2022-09-08_at_3.09.06_PM.jpeg)

De esta manera se puede ver que si bien los voltajes no son iguales se diferencian por unos pocos decimales, esta diferencia de voltajes se puede deber al estado de los componentes, el multimetro  o al hecho de que el dato esta siendo transformado en valores de 12 bits lo que nos deja con un restante de valor en mili volts que puede estar siendo tomado como ruido haciendo que varien los datos en comparacion al multimetro.

### Conclusiones.

---

Para finalizar con este proyecto es necesario hablar de los beneficios y problemas que se presentaron a la hora de trabajar, siendo el mas importante beneficio los conocimientos adquiridos tras la realizacion y como estos nospermitiran realizar mas proyectos y actividades a futuro, asi una de las barreras mas grandes se presenta al momento de querer utilizar el software, ya que, si bien es simple y intuitivo requiere tener variados conocimientos de variadas areas, como por ejemplo el hecho de que se venda un microprocesador en grandes cantidades pero que para su configuracion y codificacion en el software se utilice una version mas cercana, por que la compañia no lo a catalogado como un producto oficial de su marca, es un detalle no menor que de haber ocupado otro microprocesador en la lista los procedimientos hubieran variado y los resultados tambien. 

### Referencias.

---

 

- [https://www.st.com/en/development-tools/stm32cubeide.html](https://www.st.com/en/development-tools/stm32cubeide.html)
- [https://www.profetolocka.com.ar/2021/05/04/programando-la-stm32-blue-pill-con-el-cube-ide-parte-1-instalacion-y-configuracion/](https://www.profetolocka.com.ar/2021/05/04/programando-la-stm32-blue-pill-con-el-cube-ide-parte-1-instalacion-y-configuracion/)
- [https://homepages.inf.ed.ac.uk/rbf/HIPR2/mean.htm](https://homepages.inf.ed.ac.uk/rbf/HIPR2/mean.htm)

### Autores.

---

Este proyecto fue realizado por Manuel Instroza y Jeremias Torres. 

Para la asignatura de Laboratorio de Microprocesadores realiada por Mauricio Montanares.

Gracias por su atencion. 🦖