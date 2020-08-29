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

En esta parte del  manual está basada en IDRAC6 y a la espera de que alguien nos colabore con las ventajas de IDRAC 7, 8 y 9.

IPMI es un estándar que los fabricantes implementan de forma particular para sus sistemas, por lo que estos no tienen por qué ofrecer interoperabilidad entre ellos.
De esta forma, IPMI puede ser accedido/consultado tanto de formal local o mediante LAN. Por cuestiones de seguridad, la operación por intermedio de LAN esta deshabilitada por defecto. Para el acceso/operación local, en el caso de DELL, se puede hacer mediante una aplicación de consola llamada RACADM. Con esta aplicación podemos configurar en la interfaz IDRAC, cosas tales como dirección IP, usuario de IDRAC, Contraseña, y habilitar o deshabilitar el acceso mediante LAN a IPMI (el acceso local está habilitado por defecto). 
Mediante IPMI podemos tener acceso a diferentes datos del servidor que podremos configurar dentro de NAGIOS para configurar nuestro sistema de alarmas. Para esto nos valdremos del plugin para DELL ofrecido por la empresa alemana “Thomas-Krenn“ ([Plugin Details](https://exchange.nagios.org/directory/Plugins/Hardware/Server-Hardware/IPMI-Sensor-Monitoring-Plugin/details)).

A continuación describiré el proceso de instalación de dependencias. En mi caso mi sistema de referencia es UBUNTU, aunque la adaptación a otras distros no debería representar un problema.

###### Dependencias
* Nagios
* sudo apt-get install -y freeipmi-tools
* sudo apt-get install -y libipc-run-perl

###### Instalando el Plugin
wget https://github.com/thomas-krenn/check_ipmi_sensor_v3/archive/master.zip
tar -zxf check_ipmi_sensor_v3-master.zip
cp  check_ipmi_sensor_v3-master/check_ipmi_sensor /usr/bin/nagios/libexec/

###### Configuracion
Ahora tendremos que obtener algunos datos para utilizar con nuestro plugin, que son los id de los objetos que queremos monnitorear.
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
Tabla 1.1 - De este listado nos interesa los datos Type e ID

```
administrador@Server:~$ sudo ipmi-sensors -L
[sudo] contraseña para administrador: 
Chassis
Chip_Set
Other_Fru
Cable_Interconnect
Terminator
System_Boot_Initiated
Boot_Error
OS_Boot
OS_Critical_Stop
Slot_Connector
System_ACPI_Power_State
Watchdog_2
Platform_Alert
Entity_Presence
Monitor_ASIC_IC
LAN
Management_Subsystem_Health
Battery
Session_Audit
Version_Change
FRU_State
OEM_Reserved
Temperature
Voltage
Current
Fan
Physical_Security
Platform_Security_Violation_Attempt
Processor
Power_Supply
Power_Unit
Cooling_Device
Other_Units_Based_Sensor
Memory
Drive_Slot
POST_Memory_Resize
System_Firmware_Progress
Event_Logging_Disabled
Watchdog_1
System_Event
Critical_Interrupt
Button_Switch
Module_Board
Microcontroller_Coprocessor
Add_In_Card
```
Tabla 1.2 - De la ayuda de ipmi-sensors =>   -L, --list-sensor-types    List sensor types.

Con estas dos imágenes extraemos que, en la implementación de IPMI con la que cuenta nuestro sistema, podremos monitorear por “tipo” solo aquellos que estén presentes tanto en la imagen 1,1 y 1,2 y utilizaremos el texto de la imagen 1.2. Para todos los que no deseemos usar “agrupación” utilizaremos el ID.

RECORDAR!
``` 
Para no tener que usar sudo en los comandos derivados de IPMIsel podemos agregar los mismos a sudoers 
de la siguiente forma:

sudo vi /etc/sudoers.d/check_ipmi_sensor
nagios ALL=(root) NOPASSWD: /usr/sbin/ipmi-sensors, /usr/sbin/ipmi-sel
```

Antes de proceder con la configuración del plugin en Nagios chequearemos desde consola:
```
administrador@Server:~$ ipmi-sensors -t Power_Supply
ID | Name          | Type         | Reading    | Units | Event
45 | Status        | Power Supply | N/A        | N/A   | 'Presence detected'
46 | Status        | Power Supply | N/A        | N/A   | 'Presence detected'
50 | PS Redundancy | Power Supply | N/A        | N/A   | 'Fully Redundant'

```
Ahora que ya verificamos la existencia de IPMI en nuestro sistema, que descargamos el plugin de Thomas Krenn y que instalamos las aplicaciones necesarias y verificamos el correcto funcionamiento de las mismas con nuestro entorno,  procederemos a explicar la configuración de Nagios.

Aqui tenemos que elegir entre dos opciones, por un lado podemos crear comandos IPMI y por otro utilizaremos NRPE. Para la primera deberemos definir en nuestro archivo /usr/local/nagios/etc/objects/commands.cfg. 
Esta configuración es para el caso del uso de IPMI a nivel local:

```
define command{
  command_name  check_ipmi_fan
  command_line  $USER1$/check_ipmi_sensor -T FAN $
}
```

Y en nuestro objeto /usr/local/nagios/etc/objects/linux.cfg
```
define host{
  use              linux-server
  host_name     centos4
  alias             centos4
  address         192.168.1.151
 
 define service{
  use                         generic-service
  host_name               centos4
  service_description     IPMI
  check_command        check_ipmi_fan
}
  
```

En el caso de que habilitemos IPMI over LAN deberemos cambiar esta configuración a:
/usr/local/nagios/etc/objects/commands.cfg

```
define command{
  command_name  check_ipmi_fan
  command_line  $USER1$/check_ipmi_sensor -H $_HOSTIPMI_IP$ -f $ARG1$ -b -T FAN
}
```
```
define host{
  use           linux-server
  host_name     centos4
  alias         centos4
  address       192.168.1.151
  _ipmi_ip      192.168.1.211
}

define service{
  use                  generic-service
  host_name            centos4
  service_description  IPMI
  check_command        check_ipmi_fan!/etc/ipmi-config/ipmi.cfg -b -T FAN
}

```

```
cat /etc/ipmi-config/ipmi.cfg
username monitoring
password ao5$snNc!
privilege-level user
ipmi-sensors-workaround-flags nochecksumcheck,noauthcodecheck

```

Si optamos por la utilización de NRPE (es la opción que más me gusta a mí) deberemos aplicar las siguientes configuraciones:
En el servidor, en nuestro objeto **/usr/local/nagios/etc/objects/linux.cfg**:

```
define service{
        use                     generic-service
        host_name               Server
        service_description     IPMI Power Supply
        check_command           check_nrpe!check_power_supply -t 45
}

define service{
        use                     generic-service
        host_name               Server
        service_description     IPMI Redundancy
        check_command           check_nrpe!check_redundancy -t 45
}

define service{
        use                     generic-service
        host_name               Server
        service_description     IPMI PS1
        check_command           check_nrpe!check_ps1 -t 45
}

define service{
        use                     generic-service
        host_name               Server
        service_description     IPMI PS2
        check_command           check_nrpe!check_ps2 -t 45
}
```
**En el cliente, en el archivo /usr/local/nagios/etc/nrpe.cfg:**

```
command[check_fans]=/usr/local/nagios/libexec/check_ipmi_sensor -T FAN --nosel
command[check_power_supply]=/usr/local/nagios/libexec/check_ipmi_sensor -T Power_Supply --nosel
command[check_redundancy]=/usr/local/nagios/libexec/check_ipmi_sensor -i 50 --nosel
command[check_ps1]=/usr/local/nagios/libexec/check_ipmi_sensor -i 45 --nosel
command[check_ps2]=/usr/local/nagios/libexec/check_ipmi_sensor -i 46 --nosel
```

**(*)[--nosel]
       turn off system event log checking via ipmi-sel. If there are
       unintentional entries in SEL, use 'ipmi-sel --clear'.
Esto es debido a que IPMI guarda las caidas de sistema en algo parecido a un event log y con --nosel accedemos al estado actual del dispositivo que estamos monitoreando. No me queda claro si al hacer 'ipmi-sel --clear' la limpeza se hace en el “event log” del servidor (idrac) o es a nivel de “local” (OS). **


Resultado:

SERVICES DOWN
![Error](https://github.com/garsiv1932/nagios-ipmi/blob/master/error.jpeg?raw=true)


SERVICES UP
![Success](https://github.com/garsiv1932/nagios-ipmi/blob/master/success.jpeg?raw=true)


**Este Documento debe tener muchos detalles, ademas de que para ser completo le faltaria que alguien haga estas pruebas con ILO de HP, quien quiera colaborar sera bien recibido.**

**Para la edicion seria interesante seguir los mismos parametros de edicion de GITHUB de los siguientes documentos:**

[GitHub Syntax](https://guides.github.com/pdfs/markdown-cheatsheet-online.pdf)
