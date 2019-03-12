2.2. Profilo bloccante RPC
==========================

2.2.1. Scenario
---------------

Lo sviluppo di una interfaccia bloccante di tipo RPC si richiede nei
casi in cui:

-  L’esecuzione del metodo M è poco onerosa computazionalmente e può
   essere portata immediatamente a termine dall’erogatore. In questi
   casi è accettabile per l’erogatore mantenere una connessione aperta
   in attesa di risposta.

-  Il contesto rende complessa l’implementazione delle modalità non
   bloccanti di cui alle sezioni successive. Ad esempio, non è possibile
   per il fruitore esporre una propria interfaccia di servizio (vedi
   Sezione 2.2) ed il fruitore non può farsi carico di mantenere il
   contesto necessario ad effettuare attesa attiva (vedi Sezione 2.3).

2.2.2. Descrizione
------------------

Figura 1. Interazione bloccante RPC

In questo profilo si ha una risposta da parte dell’erogatore contestuale
alla richiesta del fruitore. Figura 1 mostra lo schema di questa
interazione.

2.2.3. Interfaccia REST
-----------------------

Nel caso di implementazione tramite tecnologia REST, DEVONO essere
seguite le seguenti indicazioni:

-  La specifica dell’interfaccia DEVE dichiarare tutti i codici di stato
   HTTP restituiti con relativo schema della risposta, oltre che ad
   eventuali header HTTP restituiti;

-  La specifica dell’interfaccia DEVE dichiarare lo schema della
   richiesta insieme ad eventuali header HTTP richiesti;

-  Al passo (1) il fruitore DEVE utilizzare come verbo HTTP per
   l’esecuzione della chiamata a procedura il verbo HTTP POST su un URL
   contenente gli ID interessati ed il nome del metodo;

-  Al passo (2) il fruitore DEVE utilizzare lo stato 200 OK a meno che
   non si verifichino errori.

Il corpo dei messaggi scambiati durante l’interazione deve seguire lo
standard JSON.

2.2.3.1. Regole di processamento
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Al termine del processamento della richiesta, l’erogatore deve fare uso
dei codici di stato HTTP rispettandone la semantica [1]_. In
particolare, al ricevimento della richiesta da parte del fruitore,
l’erogatore DEVE almeno:

-  Verificare la validità sintattica dei dati in ingresso. In caso di
   dati errati deve restituire il codice HTTP 400 Bad Request fornendo
   nel body di risposta dettagli circa l’errore;

-  Nel caso in cui qualcuno degli ID nel path o nel body non esista,
   DEVE restituire il codice 404 Not Found, indicando nel body di
   risposta quale degli ID è mancante;

-  In caso di errori non dipendenti dal fruitore, DEVE restituire i
   codici HTTP 5XX rispettando la semantica degli stessi ed indicando
   nel body di risposta il motivo dell’errore;

-  In caso di successo restituire il codice HTTP 200 OK, riempiendo il
   body di risposta con il risultato dell’operazione.

2.2.3.2. Esempio
~~~~~~~~~~~~~~~~

+-----------------------------------+-----------------------------------+
| Specifica Servizio                | https://api.amministrazioneesempi |
|                                   | o.it/rest/v1/nomeinterfacciaservi |
|                                   | zio/RESTblocking.yaml             |
+-----------------------------------+-----------------------------------+
| openapi: 3.0.1                    |                                   |
| info:                             |                                   |
| title: RESTblocking               |                                   |
| license:                          |                                   |
| name: Apache 2.0 License          |                                   |
| url:                              |                                   |
| http://www.apache.org/licenses/LI |                                   |
| CENSE-2.0.html                    |                                   |
| version: "1.0"                    |                                   |
| paths:                            |                                   |
| /resources/{id_resource}/M:       |                                   |
| post:                             |                                   |
| description: Esegue M             |                                   |
| operationId: M                    |                                   |
| parameters:                       |                                   |
| - name: id_resource               |                                   |
| in: path                          |                                   |
| required: true                    |                                   |
| schema:                           |                                   |
| type: integer                     |                                   |
| format: int32                     |                                   |
| requestBody:                      |                                   |
| content:                          |                                   |
| application/json:                 |                                   |
| schema:                           |                                   |
| $ref:                             |                                   |
| '#/components/schemas/MType'      |                                   |
| responses:                        |                                   |
| 500:                              |                                   |
| description: Errore interno       |                                   |
| avvenuto                          |                                   |
| content:                          |                                   |
| application/json:                 |                                   |
| schema:                           |                                   |
| $ref:                             |                                   |
| '#/components/schemas/ErrorMessag |                                   |
| e'                                |                                   |
| 404:                              |                                   |
| description: Identificativo non   |                                   |
| trovato                           |                                   |
| content:                          |                                   |
| application/json:                 |                                   |
| schema:                           |                                   |
| $ref:                             |                                   |
| '#/components/schemas/ErrorMessag |                                   |
| e'                                |                                   |
| 400:                              |                                   |
| description: Richiesta non valida |                                   |
| content:                          |                                   |
| application/json:                 |                                   |
| schema:                           |                                   |
| $ref:                             |                                   |
| '#/components/schemas/ErrorMessag |                                   |
| e'                                |                                   |
| 200:                              |                                   |
| description: Esecuzione di M      |                                   |
| avvenuta con successo             |                                   |
| content:                          |                                   |
| application/json:                 |                                   |
| schema:                           |                                   |
| $ref:                             |                                   |
| '#/components/schemas/MResponseTy |                                   |
| pe'                               |                                   |
| components:                       |                                   |
| schemas:                          |                                   |
| MType:                            |                                   |
| type: object                      |                                   |
| properties:                       |                                   |
| a:                                |                                   |
| $ref:                             |                                   |
| '#/components/schemas/AComplexTyp |                                   |
| e'                                |                                   |
| b:                                |                                   |
| type: string                      |                                   |
| MResponseType:                    |                                   |
| type: object                      |                                   |
| properties:                       |                                   |
| c:                                |                                   |
| type: string                      |                                   |
| AComplexType:                     |                                   |
| type: object                      |                                   |
| properties:                       |                                   |
| a1s:                              |                                   |
| type: array                       |                                   |
| items:                            |                                   |
| type: integer                     |                                   |
| format: int32                     |                                   |
| a2:                               |                                   |
| type: string                      |                                   |
| ErrorMessage:                     |                                   |
| type: object                      |                                   |
| properties:                       |                                   |
| error_message:                    |                                   |
| type: string                      |                                   |
+-----------------------------------+-----------------------------------+

Di seguito un esempio di chiamata al metodo M.

+-----------------------------------+-----------------------------------+
| HTTP Operation                    | POST                              |
+-----------------------------------+-----------------------------------+
| Endpoint                          | `https://api.amministrazioneesemp |
|                                   | io.it/rest/v1/nomeinterfacciaserv |
|                                   | izio/resources/1234/M <https://ap |
|                                   | i.amministrazioneesempio.it/rest/ |
|                                   | v1/nomeinterfacciaservizio/M>`__  |
+-----------------------------------+-----------------------------------+
| (1) Request                       | {                                 |
| Body                              |                                   |
|                                   | "a": {                            |
|                                   |                                   |
|                                   | "a1s": [1,2],                     |
|                                   |                                   |
|                                   | "a2":                             |
|                                   | "RGFuJ3MgVG9vbHMgYXJlIGNvb2wh"    |
|                                   |                                   |
|                                   | },                                |
|                                   |                                   |
|                                   | "b": "Stringa di esempio"         |
|                                   |                                   |
|                                   | }                                 |
+-----------------------------------+-----------------------------------+
| (2) Response                      | {                                 |
| Body (HTTP Status Code 200 OK)    |                                   |
|                                   | "c" : "risultato"                 |
|                                   |                                   |
|                                   | }                                 |
+-----------------------------------+-----------------------------------+
| (2) Response                      | {                                 |
| Body (HTTP Status Code 500        |                                   |
| Internal Server Error)            | "error_message" : "messaggio di   |
|                                   | errore"                           |
|                                   |                                   |
|                                   | }                                 |
+-----------------------------------+-----------------------------------+
| (2) Response                      | {                                 |
| Body (HTTP Status Code 404 Not    |                                   |
| Found)                            | "error_message" : "la risorsa     |
|                                   | 1234 non esiste"                  |
|                                   |                                   |
|                                   | }                                 |
+-----------------------------------+-----------------------------------+

2.2.4. Interfaccia SOAP
-----------------------

Se il profilo viene implementato con tecnologia SOAP, a differenza del
caso REST, il metodo invocato non è specificato nell’endpoint chiamato,
poichè viene identificato all’interno del body. Inoltre tutti gli ID
coinvolti DEVONO essere riportati all’interno del body. Si richiede che
il contenuto informativo del messaggio venga espresso mediante XML.

.. _regole-di-processamento-1:

2.2.4.1. Regole di processamento
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Nel caso di errore il WS-I Basic Profile Version 2.0 richiede l’utilizzo
del meccanismo della SOAP fault per descrivere i dettagli dell’errore.
Al ricevimento della richiesta da parte del fruitore, l’erogatore:

-  DEVE verificare la validità sintattica dei dati in ingresso. In caso
   di dati errati deve restituire il codice HTTP 500 fornendo dettagli
   circa l’errore utilizzando il meccanismo della SOAP fault;

-  Nel caso in cui qualcuno degli ID nel path o nel body non esista,
   DEVE restituire il codice HTTP 500 indicando tramite la SOAP fault
   quale degli ID è mancante;

-  In caso di errori non dipendenti dal fruitore, DEVE restituire il
   codice HTTP 500, indicando il motivo dell’errore nella SOAP fault;

-  In caso di successo restituire il codice HTTP 200 OK, riempiendo il
   body di risposta con il risultato dell’operazione.

.. _esempio-1:

2.2.4.2. Esempio
~~~~~~~~~~~~~~~~

+-----------------------------------+-----------------------------------+
| Specifica Servizio                | https://api.amministrazioneesempi |
|                                   | o.it/soap/nomeinterfacciaservizio |
|                                   | /v1?wsdl                          |
+-----------------------------------+-----------------------------------+
| <wsdl:definitions                 |                                   |
| xmlns:xsd="http://www.w3.org/2001 |                                   |
| /XMLSchema"                       |                                   |
| xmlns:wsdl="http://schemas.xmlsoa |                                   |
| p.org/wsdl/"                      |                                   |
| xmlns:tns="http://amministrazione |                                   |
| esempio.it/nomeinterfacciaservizi |                                   |
| o"                                |                                   |
| xmlns:soap="http://schemas.xmlsoa |                                   |
| p.org/wsdl/soap/"                 |                                   |
| xmlns:ns1="http://schemas.xmlsoap |                                   |
| .org/soap/http"                   |                                   |
| name="SOAPBlockingImplService"    |                                   |
| targetNamespace="http://amministr |                                   |
| azioneesempio.it/nomeinterfaccias |                                   |
| ervizio">                         |                                   |
| <wsdl:types>                      |                                   |
| <xs:schema                        |                                   |
| xmlns:xs="http://www.w3.org/2001/ |                                   |
| XMLSchema"                        |                                   |
| xmlns:tns="http://amministrazione |                                   |
| esempio.it/nomeinterfacciaservizi |                                   |
| o"                                |                                   |
| attributeFormDefault="unqualified |                                   |
| "                                 |                                   |
| elementFormDefault="unqualified"  |                                   |
| targetNamespace="http://amministr |                                   |
| azioneesempio.it/nomeinterfaccias |                                   |
| ervizio">                         |                                   |
| <xs:element name="M"              |                                   |
| type="tns:M"/>                    |                                   |
| <xs:element name="MResponse"      |                                   |
| type="tns:MResponse"/>            |                                   |
| <xs:complexType name="M">         |                                   |
| <xs:sequence>                     |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="M" type="tns:mType"/>       |                                   |
| </xs:sequence>                    |                                   |
| </xs:complexType>                 |                                   |
| <xs:complexType name="mType">     |                                   |
| <xs:sequence>                     |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="oId" type="xs:int"/>        |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="a"                          |                                   |
| type="tns:aComplexType"/>         |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="b" type="xs:string"/>       |                                   |
| </xs:sequence>                    |                                   |
| </xs:complexType>                 |                                   |
| <xs:complexType                   |                                   |
| name="aComplexType">              |                                   |
| <xs:sequence>                     |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="a1s"                        |                                   |
| type="tns:a1ComplexType"/>        |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="a2" type="xs:string"/>      |                                   |
| </xs:sequence>                    |                                   |
| </xs:complexType>                 |                                   |
| <xs:complexType                   |                                   |
| name="a1ComplexType">             |                                   |
| <xs:sequence>                     |                                   |
| <xs:element maxOccurs="unbounded" |                                   |
| minOccurs="0" name="a1"           |                                   |
| nillable="true"                   |                                   |
| type="xs:string"/>                |                                   |
| </xs:sequence>                    |                                   |
| </xs:complexType>                 |                                   |
| <xs:complexType name="MResponse"> |                                   |
| <xs:sequence>                     |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="return"                     |                                   |
| type="tns:mResponseType"/>        |                                   |
| </xs:sequence>                    |                                   |
| </xs:complexType>                 |                                   |
| <xs:complexType                   |                                   |
| name="mResponseType">             |                                   |
| <xs:sequence>                     |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="c" type="xs:string"/>       |                                   |
| </xs:sequence>                    |                                   |
| </xs:complexType>                 |                                   |
| <xs:complexType                   |                                   |
| name="errorMessageFault">         |                                   |
| <xs:sequence>                     |                                   |
| <xs:element minOccurs="0"         |                                   |
| name="customFaultCode"            |                                   |
| type="xs:string"/>                |                                   |
| </xs:sequence>                    |                                   |
| </xs:complexType>                 |                                   |
| <xs:element                       |                                   |
| name="ErrorMessageFault"          |                                   |
| nillable="true"                   |                                   |
| type="tns:errorMessageFault"/>    |                                   |
| </xs:schema>                      |                                   |
| </wsdl:types>                     |                                   |
| <wsdl:message name="MResponse">   |                                   |
| <wsdl:part                        |                                   |
| element="tns:MResponse"           |                                   |
| name="parameters"> </wsdl:part>   |                                   |
| </wsdl:message>                   |                                   |
| <wsdl:message                     |                                   |
| name="ErrorMessageException">     |                                   |
| <wsdl:part                        |                                   |
| element="tns:ErrorMessageFault"   |                                   |
| name="ErrorMessageException">     |                                   |
| </wsdl:part>                      |                                   |
| </wsdl:message>                   |                                   |
| <wsdl:message name="M">           |                                   |
| <wsdl:part element="tns:M"        |                                   |
| name="parameters"> </wsdl:part>   |                                   |
| </wsdl:message>                   |                                   |
| <wsdl:portType                    |                                   |
| name="SOAPBlockingImpl">          |                                   |
| <wsdl:operation name="M">         |                                   |
| <wsdl:input message="tns:M"       |                                   |
| name="M"> </wsdl:input>           |                                   |
| <wsdl:output                      |                                   |
| message="tns:MResponse"           |                                   |
| name="MResponse"> </wsdl:output>  |                                   |
| <wsdl:fault                       |                                   |
| message="tns:ErrorMessageExceptio |                                   |
| n"                                |                                   |
| name="ErrorMessageException">     |                                   |
| </wsdl:fault>                     |                                   |
| </wsdl:operation>                 |                                   |
| </wsdl:portType>                  |                                   |
| <wsdl:binding                     |                                   |
| name="SOAPBlockingImplServiceSoap |                                   |
| Binding"                          |                                   |
| type="tns:SOAPBlockingImpl">      |                                   |
| <soap:binding style="document"    |                                   |
| transport="http://schemas.xmlsoap |                                   |
| .org/soap/http"/>                 |                                   |
| <wsdl:operation name="M">         |                                   |
| <soap:operation soapAction=""     |                                   |
| style="document"/>                |                                   |
| <wsdl:input name="M">             |                                   |
| <soap:body use="literal"/>        |                                   |
| </wsdl:input>                     |                                   |
| <wsdl:output name="MResponse">    |                                   |
| <soap:body use="literal"/>        |                                   |
| </wsdl:output>                    |                                   |
| <wsdl:fault                       |                                   |
| name="ErrorMessageException">     |                                   |
| <soap:fault                       |                                   |
| name="ErrorMessageException"      |                                   |
| use="literal"/>                   |                                   |
| </wsdl:fault>                     |                                   |
| </wsdl:operation>                 |                                   |
| </wsdl:binding>                   |                                   |
| <wsdl:service                     |                                   |
| name="SOAPBlockingImplService">   |                                   |
| <wsdl:port                        |                                   |
| binding="tns:SOAPBlockingImplServ |                                   |
| iceSoapBinding"                   |                                   |
| name="SOAPBlockingImplPort">      |                                   |
| <soap:address                     |                                   |
| location="http://localhost:8080/s |                                   |
| oap/nomeinterfacciaservizio/v1"/> |                                   |
| </wsdl:port>                      |                                   |
| </wsdl:service>                   |                                   |
| </wsdl:definitions>               |                                   |
+-----------------------------------+-----------------------------------+

A seguire un esempio di chiamata al metodo M.

+-----------------------------------+-----------------------------------+
| Endpoint                          | https://api.amministrazioneesempi |
|                                   | o.it/soap/nomeinterfacciaservizio |
|                                   | /v1                               |
+-----------------------------------+-----------------------------------+
| Method                            | M                                 |
+-----------------------------------+-----------------------------------+
| (1) Request Body                  | | <?xml version="1.0"?>           |
|                                   | | <soap:Envelope                  |
|                                   | | xmlns:soap="http://www.w3.org/2 |
|                                   | 003/05/soap-envelope/"            |
|                                   | | soap:encodingStyle="http://www. |
|                                   | w3.org/2003/05/soap-encoding">    |
|                                   | | <soap:Header>                   |
|                                   |                                   |
|                                   | <!--Autenticazione-->             |
|                                   |                                   |
|                                   | </soap:Header>                    |
|                                   |                                   |
|                                   | | <soap:Body                      |
|                                   |   xmlns:m="http://api.amministraz |
|                                   | ioneesempio.it/nomeinterfacciaser |
|                                   | vizio">                           |
|                                   | | <m:M>                           |
|                                   |                                   |
|                                   | <m:oId>1234</m:oId>               |
|                                   |                                   |
|                                   | <m:a>                             |
|                                   |                                   |
|                                   | <m:a1s><a1>1</a1>...<a1>2</a1></m |
|                                   | :a1s>                             |
|                                   |                                   |
|                                   | <m:a2>RGFuJ3MgVG9vbHMgYXJlIGNvb2w |
|                                   | h</m:a2>                          |
|                                   |                                   |
|                                   | </m:a>                            |
|                                   |                                   |
|                                   | <m:b>Stringa di esempio</m:b>     |
|                                   |                                   |
|                                   | | </m:M>                          |
|                                   | | </soap:Body>                    |
|                                   | | </soap:Envelope>                |
+-----------------------------------+-----------------------------------+
| (2) Response Body (HTTP status    | | <?xml version="1.0"?>           |
| code 200 OK)                      | | <soap:Envelope                  |
|                                   | | xmlns:soap="http://www.w3.org/2 |
|                                   | 003/05/soap-envelope/"            |
|                                   | | soap:encodingStyle="http://www. |
|                                   | w3.org/2003/05/soap-encoding">    |
|                                   | | <soap:Body                      |
|                                   |   xmlns:m="http://amministrazione |
|                                   | esempio.it/nomeinterfacciaservizi |
|                                   | o">                               |
|                                   | | <m:MResponse>                   |
|                                   |                                   |
|                                   | <return>                          |
|                                   |                                   |
|                                   | <m:c>OK</m:c>                     |
|                                   |                                   |
|                                   | </return>                         |
|                                   |                                   |
|                                   | | </m:MResponse>                  |
|                                   | | </soap:Body>                    |
|                                   | | </soap:Envelope>                |
+-----------------------------------+-----------------------------------+
| (2) Response Body (HTTP status    | <soap:Envelope                    |
| code 500 Internal Server Error)   | xmlns:soap="http://schemas.xmlsoa |
|                                   | p.org/soap/envelope/">            |
|                                   | <soap:Body>                       |
|                                   | <soap:Fault>                      |
|                                   | <faultcode>soap:Server</faultcode |
|                                   | >                                 |
|                                   | <faultstring>Error</faultstring>  |
|                                   | <detail>                          |
|                                   | <ns2:ErrorMessageFault            |
|                                   | xmlns:ns2="http://amministrazione |
|                                   | esempio.it/nomeinterfacciaservizi |
|                                   | o">                               |
|                                   | <customFaultCode>1234</customFaul |
|                                   | tCode>                            |
|                                   | </ns2:ErrorMessageFault>          |
|                                   | </detail>                         |
|                                   | </soap:Fault>                     |
|                                   | </soap:Body>                      |
|                                   | </soap:Envelope>                  |
+-----------------------------------+-----------------------------------+

.. [1]
   http://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml