---
layout: post
title: IBM SPSS Collaboration and Deplyment Services Scoring Service Security Header
tags: [Collaboration and Deplyment Services]
---
Документацията на IBM за извикване на Real-Time Scoring услугата на продукта им SPSS Collaboration and Deplyment Services или за кратко CADS, изглежда доста добре, но в нея са пропуснали да опишат някои дребни, но много същественни детайли. Ако отворим документа "Scoring Services Developers Guide", в него много бързо ще открием адреса на WSDL-а описващ услугите, а именно:
```
http://<cads-server-host-name>:<port-number>/scoring/services/Scoring.HttpV2?wsdl
```
Също така може да видим, че за да извикваме определена scoring конфигурация, трябва да използваме операцията getScore. От своя страна операцията полуава параметър scoreRequest чиито атрибути също са описани подробно. Даден е подробен пример за извикване на услугата програмно с Java, .NET и дуги клиенти. Също така има разпечатка на SOAP request-а, които се подава. По-долу може да видите извадка от примерена SOAP заявка.

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <soapenv:Header>
        <wsse:Security soapenv:actor="http://schemas.xmlsoap.org/soap/actor/next" soapenv:mustUnderstand="0" 
                             xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd">
            <wsse:UsernameToken xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">             
                                 <wsse:Username>Native//admin</wsse:Username>
                <wsse:Password wsse:Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordText">pass</wsse:Password>
                <wsse:Nonce>ofOShsZMlgHcdD0o6A8PkQ==</wsse:Nonce>
                <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2009-01-08T20:36:10Z</wsu:Created>
            </wsse:UsernameToken>
        </wsse:Security>
        <ns1:client-accept-language soapenv:actor="http://schemas.xmlsoap.org/soap/actor/next" soapenv:mustUnderstand="0" 
                                          xmlns:ns1="http://xml.spss.com/ws/headers">en-US;q=1.0,en;q=0.8</ns1:client-accept-language>
    </soapenv:Header>
    <soapenv:Body>
        <getScore xmlns="http://xml.spss.com/scoring-v2/remote">
            <scoreRequest id="config-glm" xmlns="http://xml.spss.com/scoring-v2">
                <requestInputTable name="Table1">
                    <requestInputRow>
                        <input name="happy" value="1"></input>
                        <input name="prestg80" value="1"></input>
                        <input name="tax" value="1"></input>
                    </requestInputRow>
                </requestInputTable>
            </scoreRequest>
        </getScore>
    </soapenv:Body>
</soapenv:Envelope>
```
Обърнете внимание на Security хедъра. За него също има описание и то е следното:<br>

Username - The xml value represents the identity of the user.<br>
Password  - The attribute Type specifies the type of password.<br>

Това което никъде не e описано, е това, че като стойност за Username трябва да се подава Native//<USERNAME> когато използваме, вътрешни за CADS потребители. Ако имаме връзка към активна директория стойността трябва да бъде: 
```
<SECURITY_PROVIDER_ID>/<DOMAIN_NAME>/<USERNAME>
```
 Хм. А откъде да вземем това `<SECURITY_PROVIDER_ID>`? 
 Оказва се, че има услга Security Providers, със следният адрес:

```
http://<cads-server-host-name>:<port-number>/security-ws/services/ProviderInformation/WEB-INF/wsdl/security.wsdl
```
Услугата има опеарция getProviders, която ни връща необходимата информация, за да можем да извикваме scoring услугата. Чудите ли се какво да подадете като стойност на атрибута Nonce? Ами оказва се, че не се използва изобщо и там можете да напишете каквото си искате.