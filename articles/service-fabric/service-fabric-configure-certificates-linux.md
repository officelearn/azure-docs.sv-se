---
title: Konfigurera certifikat för Azure Service Fabric-program på Linux | Microsoft Docs
description: Konfigurera certifikat för din app med Service Fabric-körningen på ett Linux-kluster
services: service-fabric
documentationcenter: NA
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: v-jamebr
ms.openlocfilehash: 97f33a1c0c42b534dafd1e4ed378b655b339395a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42058261"
---
# <a name="certificates-and-security-on-linux-clusters"></a>Certifikat och säkerhet i Linux-kluster

Den här artikeln innehåller information om hur du konfigurerar X.509-certifikat i Linux-kluster.

## <a name="location-and-format-of-x509-certificates-on-linux-nodes"></a>Plats och format för X.509-certifikat på Linux-noder

Service Fabric Allmänt förväntar X.509-certifikat måste finnas i den */var/lib/sfcerts* på Linux-klusternoderna. Detta gäller klustercertifikat, klientcertifikat osv. I vissa fall kan du ange en plats än den *var/lib/sfcerts* mapp för certifikat. Du kan till exempel ange en annan plats via konfigurationspaketet (Settings.xml) för vissa programspecifika certifikat med Reliable Services som skapats med hjälp av Service Fabric Java SDK. Mer information finns i [certifikat som refereras till i konfigurationspaketet (Settings.xml)](#certificates-referenced-in-the-configuration-package-settingsxml).

Service Fabric förväntar certifikat måste finnas som antingen en .pem-fil som innehåller både certifikat och privat nyckel eller en .crt-fil som innehåller certifikatet och en .key-fil som innehåller den privata nyckeln för Linux-kluster. Alla filer måste vara i PEM-format. 

Om du installerar certifikatet från Azure Key Vault genom att använda antingen en [Resource Manager-mall](./service-fabric-cluster-creation-create-template.md) eller [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/?view=latest#service_fabric) kommandon, certifikatet har installerats i rätt format i den */var/ lib/sfcerts* katalogen på varje nod. Om du installerar ett certifikat via en annan metod, måste du kontrollera att certifikatet är korrekt installerad på klusternoderna.

## <a name="certificates-referenced-in-the-application-manifest"></a>Certifikat som refereras till i applikationsmanifestet

Certifikat som har angetts i programmet för Manifestets, till exempel via den [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) eller [ **EndpointCertificate** ](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-service-model-schema-elements#endpointcertificate-element)element, måste finnas i den */var/lib/sfcerts* directory. De element som används för att ange certifikat i applikationsmanifestet träder inte attributet sökväg så att certifikat som måste finnas i standardkatalogen. De här elementen tar en valfri **X509StoreName** attribut. Standardvärdet är ”Mina”, som pekar mot den */var/lib/sfcerts* på Linux-noder. Andra värden är odefinierade för ett Linux-kluster. Vi rekommenderar att du tar bort den **X509StoreName** attribut för appar som körs på Linux-kluster. 

## <a name="certificates-referenced-in-the-configuration-package-settingsxml"></a>Certifikat som refereras till i konfigurationspaketet (Settings.xml)

För vissa tjänster kan du konfigurera X.509-certifikat i den [ConfigPackage](./service-fabric-application-and-service-manifests.md) (som standard Settings.xml). Detta är exempelvis fallet när du deklarerar certifikat som används för att säkra RPC-kanaler för Reliable Services-tjänster som skapats med Service Fabric .NET Core eller Java-SDK: er. Det finns två sätt att referens certifikat i konfigurationspaketet. Stöd för varierar mellan .NET Core och Java SDK: er.

### <a name="using-x509-securitycredentialstype"></a>Med hjälp av X509 SecurityCredentialsType

Med .NET eller Java-SDK: er, kan du ange **X509** för den **SecurityCredentialsType**. Detta motsvarar den `X509Credentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.x509credentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials)) typ av `SecurityCredentials` ([.NET](https://msdn.microsoft.com/library/system.fabric.securitycredentials.aspx)/[Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)).

Den **X509** referens hittar certifikatet i ett certifikatarkiv. Följande XML visar de parametrar som används för att ange platsen för certifikatet:

```xml
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
```

För en tjänst som körs på Linux, **LocalMachine**/**min** pekar på standardplatsen för certifikat, den */var/lib/sfcerts* directory. För Linux, andra kombinationer av **CertificateStoreLocation** och **Certifikatlagringsplats** är odefinierad. 

Ange alltid **LocalMachine** för den **CertificateStoreLocation** parametern. Det finns inget behov av att ange den **certifikatarkiv** parametern eftersom standard ”My”. Med en **X509** referens, certifikatdatabasen måste finnas i den */var/lib/sfcerts* på klusternoden.  

I följande XML-visas en **TransportSettings** avsnitt baserat på det här formatet:

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

### <a name="using-x5092-securitycredentialstype"></a>Med hjälp av X509_2 SecurityCredentialsType

Med Java-SDK kan du ange **X509_2** för den **SecurityCredentialsType**. Detta motsvarar den `X509Credentials2` ([Java](https://docs.microsoft.com/java/api/system.fabric._x509_credentials2)) typ av `SecurityCredentials` ([Java](https://docs.microsoft.com/java/api/system.fabric._security_credentials)). 

Med en **X509_2** referens, du anger en sökvägsparameter så att du kan hitta certifikatet i en katalog än */var/lib/sfcerts*.  Följande XML visar de parametrar som används för att ange platsen för certifikatet: 

```xml
     <Parameter Name="SecurityCredentialsType" Value="X509_2" />
     <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
```

I följande XML-visas en **TransportSettings** avsnitt baserat på det här formatet.

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
> Certifikatet har angetts som en .crt-fil i den föregående XML. Detta innebär att det finns också en .key-fil som innehåller den privata nyckeln på samma plats.

## <a name="configure-a-reliable-services-app-to-run-on-linux-clusters"></a>Konfigurera en Reliable Services-app som körs i Linux-kluster

Service Fabric SDK: er kan du kommunicera med Service Fabric-körningen API: er att utnyttja plattformen. När du kör alla program som använder den här funktionen i säkert Linux-kluster, måste du konfigurera ditt program med ett certifikat som kan användas för att verifiera med Service Fabric-körningen. Program som innehåller Service Fabric Reliable Service-tjänster som skrivits med SDK för Java eller .NET Core kräver den här konfigurationen. 

Om du vill konfigurera ett program, lägger du till en [ **SecretsCertificate** ](https://docs.microsoft.com/azure/service-fabric/service-fabric-service-model-schema-elements#secretscertificate-element) elementet under den **certifikat** tagg, som finns under den **ApplicationManifest**  tagga i den *ApplicationManifest.xml* fil. Följande XML visar ett certifikat som refereras av dess tumavtryck: 

```xml
   <Certificates>
       <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="0A00AA0AAAA0AAA00A000000A0AA00A0AAAA00" />
   </Certificates>   
```

Du kan referera till klustercertifikatet eller ett certifikat som du installerar på varje nod i klustret. På Linux, certifikatdatabasen måste finnas i den */var/lib/sfcerts* directory. Mer information finns i [plats och format för X.509-certifikat på Linux-noder](#location-and-format-of-x509-certificates-on-linux-nodes).



