---
layout: post
title: IBM Integration Bus Keystore and Trustore Config
tags: [IBM Integration Bus, Keystore]
---

IBM Integration Bus няма keystore файл за съхранение на сертификати по подразбиране. Това е проблем ако икаме услугите ни да бъдат сервирани на https или да консумираме услуги на https. Ето как да конфигурираме keystore за IIB.   

## Създаване на keystore

С инструмента на Java keytool създаваме нов файл от тип jks които да изпозваме за съхранение на сертификатите. При положение, че имаме keytool може директно да изпълним следните команди:
```
keytool -genkey -alias DUMMY -keystore jssecacerts.jks -storetype jks -dname "CN=DUMMY,OU=BROKER,O=IBM,L=Hursley,C=GB" -storepass changeit -keypass changeit

keytool -delete -alias DUMMY -keystore jssecacerts.jks -storepass changeit
```
Сега вече имам съсздаден keystore и може да го изпозваме в IIB

## Указваме на IIB кой keystore да ползва

С командите по-долу настройваме IIB да ползва новосъздаденият keystore. След това указваме този keystore да се ползва и като truststore. За keystore и truststore могат да се позват отделни файлове, но за удобство ние ползваме един.
След това указваме какъв е типът на keystore файла. Указваме паролата за keystore и за truststore. Указваме обекта password да се използва за достъпа до keystore.

```
mqsichangeproperties <NODE_NAME> -e default -o ComIbmJVMManager -n keystoreFile -v <CANONICAL_PATH>\jssecacerts.jks

mqsichangeproperties <NODE_NAME> -o BrokerRegistry -n  brokerTruststoreFile -v <CANONICAL_PATH>\jssecacerts.jks

mqsichangeproperties <NODE_NAME> -e default -o ComIbmJVMManager -n keystoreType -v JKS

mqsisetdbparms <NODE_NAME> -n brokerKeystore::password -u temp -p changeit
mqsisetdbparms <NODE_NAME> -n brokerTruststore::password -u temp -p changeit

mqsichangeproperties <NODE_NAME> -e default -o ComIbmJVMManager -n keystorePass -v brokerKeystore::password

```

## Преглед на резултата от конфигурацията

Резулатата от конфигурацията може да видим със следната команда:

```
mqsireportproperties TESTNODE_vhristov -o ComIbmJVMManager -a -e default
```



