---
title: Konfigurera certifikat för program på Linux
description: Konfigurera certifikat för din app med Service Fabric-körningen i ett Linux-kluster
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: pepogors
ms.openlocfilehash: 802e76614f51e1f6479a311e61a49d83b8125546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282580"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certifikat och säkerhet på Linux-kluster

Den här artikeln innehåller information om hur du konfigurerar X.509-certifikat på Linux-kluster.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Plats och format för X.509-certifikat på Linux-noder

Service Fabric förväntar sig i allmänhet att X.509-certifikat ska finnas i katalogen */var/lib/sfcerts* på Linux-klusternoder. Detta gäller klustercertifikat, klientcertifikat osv. I vissa fall kan du ange en annan plats än mappen *var/lib/sfcerts* för certifikat. Med Reliable Services som skapats med Hjälp av Service Fabric Java SDK kan du till exempel ange en annan plats via konfigurationspaketet (Settings.xml) för vissa programspecifika certifikat. Mer information finns [i Certifikat som refereras i konfigurationspaketet (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

För Linux-kluster förväntar sig Service Fabric att certifikat ska finnas som antingen en PEM-fil som innehåller både certifikatet och den privata nyckeln eller som en CRT-fil som innehåller certifikatet och en .key-fil som innehåller den privata nyckeln. Alla filer ska vara i PEM-format. 

Om du installerar certifikatet från Azure Key Vault med hjälp av antingen en [Resource Manager-mall](./service-fabric-cluster-creation-create-template.md) eller [PowerShell-kommandon](https://docs.microsoft.com/powershell/module/az.servicefabric/?view=azps-2.6.0) installeras certifikatet i rätt format i katalogen */var/lib/sfcerts* på varje nod. Om du installerar ett certifikat med en annan metod måste du kontrollera att certifikatet är korrekt installerat på klusternoder.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certifikat som refereras i ansökningsmanifestet

Certifikat som anges i programmanifestet, till exempel via [**elementen SecretsCertificate**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) eller [**EndpointCertificate,**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element) måste finnas i katalogen */var/lib/sfcerts.* De element som används för att ange certifikat i programmanifestet tar inte ett sökvägsattribut, så certifikaten måste finnas i standardkatalogen. Dessa element tar ett **X509StoreName-attribut** som tillval. Standardär "My", som pekar på katalogen */var/lib/sfcerts* på Linux-noder. Alla andra värden är odefinierade i ett Linux-kluster. Vi rekommenderar att du utelämnar attributet **X509StoreName** för appar som körs på Linux-kluster. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certifikat som refereras i konfigurationspaketet (Settings.xml)

För vissa tjänster kan du konfigurera X.509-certifikat i [ConfigPackage](./service-fabric-application-and-service-manifests.md) (som standard Settings.xml). Detta är till exempel fallet när du deklarerar certifikat som används för att skydda RPC-kanaler för reliable services-tjänster som skapats med Service Fabric .NET Core eller Java SDK:er. Det finns två sätt att referera till certifikat i konfigurationspaketet. Stödet varierar mellan .NET Core och Java SDK:er.

### <a name="using-x509-securitycredentialstype"></a>Använda X509 SecurityCredentialsType

WIth .NET eller Java SDK: er, kan du ange **X509** för **SecurityCredentialsType**. `X509Credentials` Detta motsvarar[(.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java)](https://docs.microsoft.com/java/api/system.fabric.x509credentials)typ `SecurityCredentials` av ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)).

**X509-referensen** lokaliserar certifikatet i ett certifikatarkiv. Följande XML visar de parametrar som används för att ange platsen för certifikatet:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

För en tjänst som körs på Linux pekar **LocalMachine**/**My** på standardplatsen för certifikat, katalogen */var/lib/sfcerts.* För Linux är alla andra kombinationer av **CertificateStoreLocation** och **CertificateStoreName** odefinierade. 

Ange alltid **LocalMachine** för parametern **CertificateStoreLocation.** Det finns ingen anledning att ange parametern **CertificateStoreName** eftersom den som standard är "My". Med en **X509-referens** måste certifikatfilerna finnas i katalogen */var/lib/sfcerts* på klusternoden.  

Följande XML visar ett **avsnittet TransportSettings** baserat på det här formatet:

```xml
<Section Name="HelloWorldStatefulTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
    <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
    <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
</Section>
```

### <a name="using-x509_2-securitycredentialstype"></a>Använda X509_2 SecurityCredentialsType

Med Java SDK kan du ange **X509_2** för **SecurityCredentialsType**. Detta motsvarar `X509Credentials2` [(Java)](https://docs.microsoft.com/java/api/system.fabric.x509credentials2)typ `SecurityCredentials` av[(Java](https://docs.microsoft.com/java/api/system.fabric.securitycredentials)). 

Med en **X509_2** referens anger du en sökvägsparameter så att du kan hitta certifikatet i en annan katalog än */var/lib/sfcerts*.  Följande XML visar de parametrar som används för att ange platsen för certifikatet: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

Följande XML visar ett **TransportSettings-avsnitt** baserat på det här formatet.

```xml
<!--Section name should always end with "TransportSettings".-->
<!--Here we are using a prefix "HelloWorldStateless".-->
<Section Name="HelloWorldStatelessTransportSettings">
    <Parameter Name="MaxMessageSize" Value="10000000" />
    <Parameter Name="SecurityCredentialsType" Value="X509_2" />
    <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
    <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
</Section>
```

> [!NOTE]
> Certifikatet anges som en CRT-fil i föregående XML. Detta innebär att det också finns en .key-fil som innehåller den privata nyckeln på samma plats.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurera en reliable services-app så att den körs på Linux-kluster

Med Service Fabric-SDK:erna kan du kommunicera med API:erna för service fabric-körning för att utnyttja plattformen. När du kör alla program som använder den här funktionen på säkra Linux-kluster måste du konfigurera programmet med ett certifikat som det kan använda för att validera med Service Fabric-körningen. Program som innehåller serviceprogram reliable service-tjänster som skrivs med .NET Core eller Java SDK:er kräver den här konfigurationen. 

Om du vill konfigurera ett program lägger du till ett [**SecretsCertificate-element**](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) under taggen **Certifikat,** som finns under **applicationmanifest-taggen** i filen *ApplicationManifest.xml.* Följande XML visar ett certifikat som refereras av tumavtrycket: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Du kan referera antingen till klustercertifikatet eller ett certifikat som du installerar på varje klusternod. På Linux måste certifikatfilerna finnas i katalogen */var/lib/sfcerts.* Mer information finns i [Plats och format för X.509-certifikat på Linux-noder](#location-and-format-of-x509-certificates-on-linux-nodes).



