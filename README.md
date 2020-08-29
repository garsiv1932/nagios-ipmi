## ¿Qué es IPMI (Intelligent Platform Management Interface)?


Es un gestor de interfaz que nos permite gestionar servidores con independencia de
sistemas operativos y tipos de CPU. 
¿Cómo Funciona?
Funciona con independencia del sistema operativo, los administradores pueden acceder a los
sistemas y restablecerlos aún en el caso de que el sistema operativo no responda.
¿Qué ventajas me puede aportar esta plataforma?
Algunas de las ventajas que nos pueden aportar esta plataforma de interfaz son:

* Fácil acceso y administración.
* Rápida Opción para reinicio remoto, apagado y encendido instantáneo para evitar tiempo fuera de línea.
* Capacidad de recuperación remota (utilizando la conexión LAN existente, sin importar el estado del sistema).
* Monitoreo del estado del hardware que ayuda a prevenir fallos.
* Independiente al servidor, siempre disponible.
* IPMI trabaja con cualquier sistema operativo.
* KVM dedicado sobre IP bajo demanda 24/7.
* Reduce tus costos de administración y es gratis en todos nuestros servidores.

### ¿Qué es KVM (Kernel-based Virtual Machine,Máquina virtual basada en el núcleo)?
Es una solución para implementar virtualización completa con Linux. Permite ejecutar máquinas virtuales utilizando imágenes de disco que contienen sistemas operativos sin modificar.

En este documento daremos por descontado que el lector ya cuenta con conocimientos de NAGIOS y NRPE.

## DELL

En esta parte del  manual esta basada en IDRAC6 y a la espera de que alguien nos colabore con las ventajas de IDRAC 7, 8 y 9.

IPMI es un estandar que los fabricantes implementan de forma particular para sus sistemas, por lo que estos no tienen porque ofrecer interoperabilidad entre ellos.
De esta forma, IPMI puede ser accedido/consultado tanto de formal local o mediante LAN. Por cuestiones de seguridad, la operacion por intermedio de LAN esta deshabilitada por defecto. Para el acceso/operacion local, en el caso de DELL, se puede hacer mediante una aplicacion de consola llamada RACADM. Con esta aplicacion podemos configrar en la interfaz IDRAC, cosas tales como direccion IP, usuario de IDRAC, Contraseña, y habilitar o deshabilitar el acceso mediante LAN a IPMI (el acceso local esta habilitado por defecto). 
Mediante IPMI podemos tener acceso a diferentes datos del servidor que podremos configurar dentro de NAGIOS para configurar nuestro sistema de alarmas. Para esto nos valdremos del plugin para DELL ofrecido por la empresa alemana “Thomas-Krenn“ (https://exchange.nagios.org/directory/Plugins/Hardware/Server-Hardware/IPMI-Sensor-Monitoring-Plugin/details).

A continuacion describire el proceso de instalacion de dependencias. En mi caso mi sistema de referencia es UBUNTU, anuque la adaptacion a otras distros no deberia representar un problema.

###### Dependencias
* Nagios
* sudo apt-get install -y freeipmi-tools
* sudo apt-get install -y libipc-run-perl

###### Instalando el Plugin
wget https://github.com/thomas-krenn/check_ipmi_sensor_v3/archive/master.zip
tar -zxf check_ipmi_sensor_v3-master.zip
cp  check_ipmi_sensor_v3-master/check_ipmi_sensor /usr/bin/nagios/libexec/

###### Configuracion
Ahora tendremos que obtener algunos datos para utillizar con nuestro plugin, que son los id de los objetos que queremos monnitorear.
```
sudo ipmimonitoring 
[sudo] contraseña para administrador: 

ID  | Name              | Type                    | State    | Reading    | Units | Event
5   | Ambient Temp      | Temperature             | Nominal  | 23.00      | C     | 'OK'
7   | CMOS Battery      | Battery                 | Nominal  | N/A        | N/A   | 'OK'
8   | VCORE             | Voltage                 | Nominal  | N/A        | N/A   | 'State Deasserted'
9   | VCORE             | Voltage                 | Nominal  | N/A        | N/A   | 'State Deasserted'
32  | iDRAC6 Ent Pres   | Entity Presence         | Nominal  | N/A        | N/A   | 'Entity Present'
33  | Stor Adapt Pres   | Entity Presence         | Nominal  | N/A        | N/A   | 'Entity Present'
34  | FAN 1 RPM         | Fan                     | Nominal  | 2400.00    | RPM   | 'OK'
35  | FAN 2 RPM         | Fan                     | Nominal  | 2520.00    | RPM   | 'OK'
36  | FAN 3 RPM         | Fan                     | Nominal  | 2400.00    | RPM   | 'OK'
37  | FAN 4 RPM         | Fan                     | Nominal  | 2520.00    | RPM   | 'OK'
43  | Status            | Processor               | Nominal  | N/A        | N/A   | 'Processor Presence detected'
44  | Status            | Processor               | Nominal  | N/A        | N/A   | 'Processor Presence detected'
45  | Status            | Power Supply            | Nominal  | N/A        | N/A   | 'Presence detected'
46  | Status            | Power Supply            | Nominal  | N/A        | N/A   | 'Presence detected'
47  | OS Watchdog       | Watchdog 2              | Nominal  | N/A        | N/A   | 'OK'
49  | Intrusion         | Physical Security       | Nominal  | N/A        | N/A   | 'OK'
50  | PS Redundancy     | Power Supply            | Nominal  | N/A        | N/A   | 'Fully Redundant'
51  | Fan Redundancy    | Fan                     | Nominal  | N/A        | N/A   | 'Fully Redundant'
53  | Drive 0           | Drive Slot              | Nominal  | N/A        | N/A   | 'Drive Presence'
54  | Drive 1           | Drive Slot              | Nominal  | N/A        | N/A   | 'Drive Presence'
55  | Drive 2           | Drive Slot              | Nominal  | N/A        | N/A   | 'Drive Presence'
56  | Drive 3           | Drive Slot              | Nominal  | N/A        | N/A   | 'Drive Presence'
57  | Drive 4           | Drive Slot              | Nominal  | N/A        | N/A   | 'OK'
58  | Drive 5           | Drive Slot              | Nominal  | N/A        | N/A   | 'OK'
59  | Drive 6           | Drive Slot              | Nominal  | N/A        | N/A   | 'OK'
60  | Drive 7           | Drive Slot              | Nominal  | N/A        | N/A   | 'OK'
69  | Cable SAS A       | Cable/Interconnect      | Nominal  | N/A        | N/A   | 'Cable/Interconnect is connected'
70  | Cable SAS B       | Cable/Interconnect      | Nominal  | N/A        | N/A   | 'Cable/Interconnect is connected'
71  | DKM Status        | OEM Reserved            | N/A      | N/A        | N/A   | 'OEM Event = 0000h'
118 | Current           | Current                 | Nominal  | 0.60       | A     | 'OK'
119 | Voltage 1         | Voltage                 | Nominal  | 226.00     | V     | 'OK'
120 | Voltage 2         | Voltage                 | Nominal  | 230.00     | V     | 'OK'
121 | System Level      | Current                 | Nominal  | 273.00     | W     | 'OK'
```

You can use the [editor on GitHub](https://github.com/garsiv1932/nagios-ipmi/edit/master/README.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/garsiv1932/nagios-ipmi/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
