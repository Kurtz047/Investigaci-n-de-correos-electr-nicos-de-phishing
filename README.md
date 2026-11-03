# Investigacion de correos electrenicos de phishing
Cuando se detecte o se reporte un correo electrónico de phishing, investigue analizando los encabezados en busca de indicadores de suplantación de identidad, inspeccionando las URL con la inteligencia sobre amenazas, verificando si algún usuario hizo clic o envió credenciales y correlacionando los registros SIEM con los de otros destinatarios. Si se enviaron credenciales o se ejecutó malware, escale el problema. Para contener la amenaza, restablezca las contraseñas y bloquee los dominios maliciosos.

# Descripción general
Las investigaciones de phishing son el tipo de alerta más común en cualquier SOC. Una empresa típica recibe cientos de intentos de phishing al día, y la diferencia entre un incidente contenido y una brecha de seguridad a menudo radica en la rapidez y exhaustividad con que un analista investiga la alerta inicial. Esta guía le acompaña a través del flujo de trabajo completo de la investigación, desde la clasificación inicial hasta la contención, abarcando el análisis de encabezados de correo electrónico, la verificación de la reputación de las URL, la evaluación del compromiso de credenciales y la detección de propagación lateral .
Aprenderá a distinguir el phishing masivo de productos básicos del spearphishing dirigido, a identificar indicadores de compromiso que justifiquen la escalada de la situación y a documentar sus hallazgos para la transferencia de la respuesta a incidentes. Las técnicas de este manual son aplicables a las principales plataformas de seguridad de correo electrónico y herramientas SIEM.

# Cuando veas esto
## 1 La puerta de enlace de seguridad de correo electrónico marca un mensaje entrante como sospechoso.

## 2 El usuario informa de un correo electrónico de phishing a través del botón de informe o del servicio de asistencia.

## 3 La alerta SIEM se activa cuando un usuario hace clic en una URL categorizada como recién registrada o de baja reputación.

## 4 XDR detecta la creación de un proceso a partir de un archivo adjunto de correo electrónico (por ejemplo, Word creando PowerShell).

## 5 Varios usuarios reciben el mismo correo electrónico sospechoso en un corto período de tiempo.

## 6 Los registros de autenticación muestran intentos de inicio de sesión desde una ubicación inconsistente con el perfil del usuario poco después de la entrega del correo electrónico.

# Pasos de la investigación
- Analizar los encabezados y metadatos del correo electrónico.
Extraiga los encabezados completos del correo electrónico y examine la infraestructura de envío. Verifique la alineación de SPF, DKIM y DMARC. Busque suplantación de identidad en el nombre del remitente, donde el nombre del remitente difiere de la dirección de envío real. Anote la dirección IP de origen y compárela con las fuentes de inteligencia sobre amenazas. Identifique si el correo electrónico se envió a un solo destinatario o forma parte de una campaña masiva examinando la lista de destinatarios y los registros de la puerta de enlace de correo electrónico.

<img width="487" height="277" alt="Captura de pantalla 2026-11-03 165858" src="https://github.com/user-attachments/assets/d6f6b53d-3913-4dbc-b77f-d7b2683eb646" />
<img width="485" height="256" alt="Captura de pantalla 2026-11-03 165910" src="https://github.com/user-attachments/assets/7ed2c3dd-0907-4030-b73a-48b74db70dfd" />
<img width="484" height="185" alt="Captura de pantalla 2026-11-03 165918" src="https://github.com/user-attachments/assets/5224af1c-d594-4fea-907f-ec991d573d92" />

- Inspeccione las URL y los archivos adjuntos.
  
Extraiga todas las URL del cuerpo del correo electrónico y de los archivos adjuntos. Analice cada URL con servicios de inteligencia sobre amenazas, buscando dominios recién registrados (< 30 días), acortadores de URL que redirigen a destinos desconocidos y dominios que suplantan direcciones de servicios legítimos. Para los archivos adjuntos, verifique los hashes de archivo con VirusTotal o servicios similares. Anote cualquier documento con macros habilitadas, archivos comprimidos protegidos con contraseña o archivos con doble extensión.

<img width="485" height="426" alt="Captura de pantalla 2026-11-03 170139" src="https://github.com/user-attachments/assets/4a173c48-9e04-4b24-ad6b-77437b7734e1" />
<img width="483" height="429" alt="Captura de pantalla 2026-11-03 170147" src="https://github.com/user-attachments/assets/53770441-9c86-48a8-b2d5-9d892cf5f610" />
<img width="485" height="382" alt="Captura de pantalla 2026-11-03 170219" src="https://github.com/user-attachments/assets/c32c5b10-893e-49aa-acce-cc3b4b82a0cf" />

- Determinar si algún usuario interactuó
Revise los registros de proxy, los registros DNS y la telemetría de los puntos finales para determinar si algún destinatario hizo clic en la URL o abrió el archivo adjunto. Verifique los registros de proxy web en busca de conexiones al dominio sospechoso. Revise los registros de detección de puntos finales en busca de eventos de creación de procesos vinculados a aplicaciones de cliente de correo electrónico. Busque indicios de envío de credenciales revisando los registros de autenticación del servicio objetivo.

<img width="483" height="450" alt="Captura de pantalla 2026-11-03 170603" src="https://github.com/user-attachments/assets/09fbfbb4-8bb5-407b-afa7-a68038ef8ad9" />
<img width="484" height="432" alt="Captura de pantalla 2026-11-03 170610" src="https://github.com/user-attachments/assets/0dc9839e-ac44-4aa8-9b0e-d223ee2ec9d0" />
<img width="484" height="378" alt="Captura de pantalla 2026-11-03 170618" src="https://github.com/user-attachments/assets/fcd7a0e6-2974-482f-93ce-11c0425660d6" />

- Evaluar el compromiso de credenciales
Si un usuario interactuó con una página de robo de credenciales, revise los registros de autenticación de la cuenta afectada. Busque inicios de sesión exitosos desde nuevas direcciones IP o ubicaciones, especialmente entre 1 y 4 horas después de la entrega del correo electrónico de phishing. Verifique si hay intentos de eludir la autenticación multifactor (MFA), inscripciones de nuevos dispositivos MFA o concesiones de consentimiento de la aplicación OAuth. Revise las reglas del buzón para el reenvío automático a direcciones externas.

<img width="484" height="469" alt="Captura de pantalla 2026-11-03 170855" src="https://github.com/user-attachments/assets/4e3f4d58-062e-4c6c-94a3-b6ce6400fb32" />
<img width="486" height="447" alt="Captura de pantalla 2026-11-03 170904" src="https://github.com/user-attachments/assets/eb35d7fd-af67-406c-8930-878eb1a370dd" />
<img width="485" height="416" alt="Captura de pantalla 2026-11-03 170924" src="https://github.com/user-attachments/assets/957e4f7e-9c14-4270-b019-87d1bb33059c" />

- Contener la amenaza.
Según los resultados obtenidos, tome las medidas de contención adecuadas. Bloquee el dominio y las URL del remitente malicioso en la puerta de enlace de correo electrónico y el proxy web. Si las credenciales se vieron comprometidas, restablezca la contraseña y revoque las sesiones activas. Si se ejecutó malware, aísle el equipo afectado. Busque y elimine el correo electrónico de phishing de todos los buzones de correo de los destinatarios mediante las herramientas de administración de correo electrónico.

<img width="486" height="168" alt="Captura de pantalla 2026-11-03 171058" src="https://github.com/user-attachments/assets/4d28ae54-ee3a-4205-b0c7-c553d99857b5" />
<img width="483" height="127" alt="Captura de pantalla 2026-11-03 171105" src="https://github.com/user-attachments/assets/8449c101-46a4-45e9-bfe7-6d25e1c1c979" />
<img width="481" height="112" alt="Captura de pantalla 2026-11-03 171118" src="https://github.com/user-attachments/assets/c6c79b26-a6f1-49f8-8168-0863e437a410" />

- Documentar los hallazgos y cerrar.
Registre todos los hallazgos de la investigación, incluyendo: origen de la alerta inicial, resultados del análisis de correo electrónico, lista de usuarios afectados, medidas de contención implementadas y recomendaciones para la prevención. Actualice el ticket con indicadores de compromiso (URL maliciosas, direcciones de remitente, hashes de archivos) para inteligencia sobre amenazas. Si se trató de un ataque dirigido, anote el patrón de selección de objetivos para el monitoreo futuro. Cierre la alerta con la clasificación correspondiente (verdadero positivo o falso positivo).

# Errores comunes
- Solo se comprueba el nombre del remitente que se muestra en lugar de examinar los encabezados completos del correo electrónico y la dirección de envío real.
  
- No buscar a otros destinatarios de la misma campaña de phishing; un informe de un usuario a menudo significa que docenas lo recibieron.
  
- Se cierra la alerta tras confirmar que no se ha hecho clic, sin bloquear el dominio malicioso para futuras protecciones.
  
- No se comprueban las reglas de reenvío automático que los atacantes añaden a los buzones de correo comprometidos para un acceso persistente.
  
- Suponiendo que la investigación se complete después del restablecimiento de la contraseña sin monitoreo para detectar accesos no autorizados continuos

# Criterios de escalamiento
- El usuario envió sus credenciales a una página de recolección de datos y se confirmó el acceso no autorizado.
  
- El malware se ejecutó en un equipo después de que se abriera el archivo adjunto.
  
- Ataques de spearphishing dirigidos contra ejecutivos o empleados con acceso privilegiado.
  
- Campaña dirigida a múltiples usuarios simultáneamente, lo que sugiere un ataque coordinado.
