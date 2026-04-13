# FibERP + Anàlisi de Logs

> Aplicació web ERP amb sistema de detecció d'intrusions (IDS) integrat.

## Descripció

**FibERP** és un projecte desenvolupat dins l'assignatura _"Projecte de Tecnologia de la Informació (PTI)"_ de la Facultat d'Informàtica de Barcelona (FIB) de la UPC.

L'objectiu principal és la creació d'una **aplicació web tipus ERP** amb un **sistema de detecció d'intrusions (IDS)** integrat per monitorar i protegir els fluxos d'informació. L'ERP actua com un generador controlat de logs, utilitzant l'activitat de l'aplicació com a font d'informació primària per al monitoratge de seguretat.

---

## Arquitectura del Sistema

L'arquitectura es distribueix en **dues màquines virtuals (VM)** per optimitzar els recursos, especialment a causa de l'ús intensiu de memòria de la intel·ligència artificial.

![Arquitectura del Sistema](arquitectura.png)

### VM1 - Aplicació i Automatització

Components desplegats mitjançant contenidors Docker:

| Component       | Descripció                                                                                                    |
| --------------- | ------------------------------------------------------------------------------------------------------------- |
| **Frontend**    | Desenvolupat amb HTML, CSS i JavaScript natiu, sense frameworks externs                                       |
| **Backend**     | Construït amb Symfony 7, gestiona la lògica de negoci i la generació de logs (`security.log` i `generic.log`) |
| **n8n**         | Plataforma low-code d'automatització que orquestra els fluxos de treball i la integració de l'IDS             |
| **Wazuh Agent** | Encarregat de capturar i enviar els logs de l'ERP cap al manager                                              |

### VM2 - IA i Seguretat

Dedicada al processament de dades i anàlisi intel·ligent:

| Component                   | Descripció                                                                                                        |
| --------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **LLM (Ollama)**            | Execució local del model Qwen 2.5:3b instruct per analitzar alertes i fitxers garantint la privadesa de les dades |
| **Wazuh Manager/Dashboard** | Centralitza la decodificació dels logs, la indexació i la generació d'alertes                                     |

---
## Interacció amb el Projecte

El projecte FibERP està format per diversos serveis accessibles via web o mitjançant SSH. Sempre amb conexió UPC  (directe o via VPN - FortiClient)
A continuació es detallen els punts d’accés a cada component per facilitar la seva exploració i validació.
Les credencials necessàries es troben documentades a l’annex de la memòria i no s’inclouen en aquest repositori.


## ERP – Aplicació Web

Interfície principal del sistema ERP, des d’on es genera l’activitat que produeix els logs de seguretat.

Accés web:
http://10.4.41.69
Funcionalitats principals:

- Autenticació i gestió d’usuaris
- Gestió de productes i comandes
- Control horari (fitxatge)¡
- Consulta de nòmines 
- Panell d’administració (usuaris amb rol d’administrador)
   
_Secció de consulta de comandes_

![Secció d'administració](web.png)

## Backend (API REST)

Servei encarregat de la lògica de negoci i de la generació de logs de seguretat.

API REST:
http://10.4.41.69:8080
Característiques:
- Endpoints protegits amb JWT
- Generació de logs security.log i generic.log
- Validació d’accions sensibles (login, pujada de fitxers, canvis de contrasenya)
- Aquest servei no disposa d’interfície gràfica i és consumit exclusivament pel frontend.

## n8n – Automatització de Fluxos

Plataforma d’automatització utilitzada per orquestrar la comunicació entre Wazuh, VirusTotal i el model d’IA.

Accés web:
http://10.4.41.69:5678/

Funcionalitats:
- Visualització i edició dels fluxos de seguretat
- Recepció d’alertes mitjançant webhooks
- Enviament de logs i alertes al model d’IA
- Generació de resums i notificacions

## Wazuh Dashboard

Interfície de monitoratge de seguretat i visualització de les alertes generades a partir dels logs de l’ERP.

Accés web:
https://10.4.41.70/app/login?

Funcionalitats:
- Visualització d’alertes de seguretat
- Consulta d’esdeveniments i logs indexats
- Verificació de l’estat de l’agent i del sistema IDS
  
## Portainer – Gestió de Contenidors

Eina gràfica per a la gestió i monitoratge dels contenidors Docker desplegats al projecte.

Accés web:
https://10.4.41.69:9443/#!/auth

Funcionalitats:
- Visualització de contenidors actius
- Gestió d’imatges i stacks
- Monitoratge de recursos i estat dels serveis

 ## Accés per SSH a les Màquines Virtuals

Per a tasques d’administració, depuració o desplegament manual, es pot accedir a les màquines virtuals mitjançant SSH:
Tots els fitxers de configuració de Wazuh es troben a /var/ossec/... 

VM1 – Aplicació i automatització:
10.4.41.69

VM2 – Seguretat i IA:
10.4.41.70

Les credencials d’accés es troben documentades a l’annex de la memòria.
