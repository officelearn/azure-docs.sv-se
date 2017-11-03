---
title: "Guide för att skapa en datatjänst för Marketplace | Microsoft Docs"
description: "Detaljerade anvisningar för hur du skapar, certifiera och distribuera en Data-tjänst för att köpa på Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 148f8638-ee80-4100-8d63-5afa4167ca1b
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 2ab624941fc385f14b62bb5d743927f157955845
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="examples-of-mapping-an-existing-web-service-to-odata-through-csdls"></a>Exempel för att matcha en befintlig webbtjänst mot OData via CSDLs
> [!IMPORTANT]
> **Just nu vi inte längre onboarding några nya Data Service-utgivare. Nya dataservices kommer inte godkännas för lista.** Om du har ett SaaS-affärsprogram som du vill publicera på AppSource hittar du mer information [här](https://appsource.microsoft.com/partners). Om du har en IaaS-program eller developer-tjänsten som du vill publicera på Azure Marketplace du hittar mer information [här](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

## <a name="example-functionimport-for-raw-data-returned-using-post"></a>Exempel: FunctionImport för ”Raw” data som returneras med ”POST”
Använd POST rådata för att skapa en ny underordnad och returnera sin server definierats URL(location) eller definierats URL Om du vill uppdatera en del av underordnad på servern.  Där är underordnat en dataström, d.v.s. Ostrukturerade, t.ex. en textfil.  Tänk efter i inte idempotent utan en plats.

        <!--  No EntitySet or EntityType nodes required for Raw output-->
        <FunctionImport Name="AddUsageEvent" ReturnType="Raw(text/plain)" d:EncodeParameterValues="true" d:AllowedHttpMethods="POST" d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Add usage event (data acquisition)</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>

## <a name="example-functionimport-using-delete"></a>Exempel: FunctionImport med ”ta bort”
Använd ta bort för att ta bort en angiven URI.

        <EntitySet Name="DeleteUsageFileEntitySet" EntityType="MyOffer.DeleteUsageFileEntity" />
        <FunctionImport Name="DeleteUsageFile" EntitySet="DeleteUsageFileEntitySet" ReturnType="Collection(MyOffer.DeleteUsageFileEntity)"  d:AllowedHttpMethods="DELETE" d:EncodeParameterValues="true” d:BaseUri=”http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643" >
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Delete usage File</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>
        <EntityType Name="DeleteUsageFileEntity" d:Map="//boolean">
        <Property Name="boolean" Type="String" Nullable="true" d:Map="./boolean" />
        </EntityType>

## <a name="example-functionimport-using-post"></a>Exempel: FunctionImport med ”publicera”
Använd POST rådata för att skapa en ny underordnad och returnera sin server definierats URL(location) eller definierats URL Om du vill uppdatera en del av underordnad på servern.  Där är underordnat en struktur. Tänk efter är inte idempotent utan en plats.

        <EntitySet Name="CreateANewModelEntitySet2" EntityType=" MyOffer.CreateANewModelEntity2" />
        <FunctionImport Name="CreateModel" EntitySet="CreateANewModelEntitySet2" ReturnType="Collection(MyOffer.CreateANewModelEntity2)" d:EncodeParameterValues="true" d:AllowedHttpMethods="POST" d:BaseUri=”http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Create A New Model</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>

## <a name="example-functionimport-using-put"></a>Exempel: FunctionImport med ”PLACERA”
Använd PUT för att skapa en ny underordnad eller definierat URL Om du vill uppdatera hela underordnat på en server.  Om underordnat är en struktur, PUT är idempotent så att flera förekomster resulterar i samma tillstånd engångsfaktorautentisering x = 5.  Placera ska användas med det fullständiga innehållet på den angivna resursen.

        <EntitySet Name="UpdateAnExistingModelEntitySet" EntityType="MyOffer.UpdateAnExistingModelEntity" />
        <FunctionImport Name="UpdateModel" EntitySet="UpdateAnExistingModelEntitySet" ReturnType="Collection(MyOffer.UpdateAnExistingModelEntity)" d:EncodeParameterValues="true" d:AllowedHttpMethods="PUT" d:BaseUri=”http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Update an Existing Model</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>
        <EntityType Name="UpdateAnExistingModelEntity" d:Map="//string">
        <Property Name="string"     Type="String" Nullable="true" d:Map="./string" />
        </EntityType>


## <a name="example-functionimport-for-raw-data-returned-using-put"></a>Exempel: FunctionImport för ”Raw” data som returneras med ”PLACERA”
Använd PUT rådata att skapa en ny underordnad eller uppdatera hela underordnat på en server som har definierats-URL.  Där är underordnat en dataström, d.v.s. Ostrukturerade, t.ex. en textfil.  PLACERA är idempotent så att flera förekomster resulterar i samma tillstånd engångsfaktorautentisering x = 5.  Placera ska användas med det fullständiga innehållet på den angivna resursen.

        <!--  No EntitySet or EntityType nodes required for Raw output-->
        <FunctionImport Name="CancelBuild” ReturnType="Raw(text/plain)" d:AllowedHttpMethods="PUT" d:EncodeParameterValues="true" d:BaseUri=” http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Cancel Build</d:Description>
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>


## <a name="example-functionimport-for-raw-data-returned-using-get"></a>Exempel: FunctionImport för ”Raw” data som returneras med ”ta”
Använd hämta rådata för att returnera en underordnad är Ostrukturerade, d.v.s. text.

        <!--  No EntitySet or EntityType nodes required for Raw output-->
        <FunctionImport Name="GetModelUsageFile" ReturnType="Raw(text/plain)" d:EncodeParameterValues="true" d:AllowedHttpMethods="GET" d:BaseUri="https://cmla.cloudapp.net/api2/model/builder/build?buildId={buildId}&amp;apiVersion={apiVersion}">
        <d:Title d:Map="" />
        <d:Rights d:Map="" />
        <d:Description>Download A Models Usage File</d:Description>
        <d:Headers>
        <d:Header d:Name="Accept" d:Value="application/xml,application/xhtml+xml,text/html;" />
        <d:Header d:Name="Content-Type" d:Value="application/xml;charset=UTF-8" />
        </d:Headers>
        <Parameter Name="name" Nullable="false" Mode="In" Type="String" d:Description="first name" d:SampleValues="John|Joe|Bill"  d:EncodeParameterValue="true" />
        <d:Namespaces>
        <d:Namespace d:Prefix="p" d:Uri="http://schemas.organization.net/2010/04/myNamespace " />
        <d:Namespace d:Prefix="p2" d:Uri=" http://schemas.organization.net/2010/04/myNamespace2 " />
        </d:Namespaces>
        </FunctionImport>

## <a name="example-functionimport-for-paging-through-returned-data"></a>Exempel: FunctionImport för ”sidindelning” via returnerade data
Använda implementera RESTful bläddring genom dina data med GET.  Standard-växling är inställd på 100 datarad per sida.

        <EntitySet Name=”CropEntitySet" EntityType="MyOffer.CropEntity" />
        <FunctionImport    Name="GetCropReport" EntitySet="CropEntitySet” ReturnType="Collection(MyOffer.CropEntity)" d:EmitSelfLink="false" d:EncodeParameterValues="true" d:Paging="SkipTake" d:MaxPageSize="100" d:BaseUri="http://api.mydata.org/Crop? report={report}&amp;series={series}&amp;start={$skip}&amp;size=100">
        <Parameter Name="report" Type="Int32" Mode="In" Nullable="false" d:SampleValues="4"  d:enum="1|2|3|4|5|6|7|8|9|10|11|12|13|14|15|16|17|18|19"  />
        <Parameter Name="series"    Type="String"    Mode="In" Nullable="false" d:SampleValues="FARM" />
        <d:Headers>
        <d:Header d:Name="Content-Type" d:Value="text/xml;charset=UTF-8" />
        </d:Headers>
        <d:Namespaces>
        <d:Namespace d:Prefix="diffgr" d:Uri="urn:schemas-microsoft-com:xml-diffgram-v1" />
        </d:Namespaces>
        </FunctionImport>

## <a name="see-also"></a>Se även
* Om du vill förstå den övergripande processen för OData-mappning och syfte, den här artikeln [mappning för OData-tjänsten](marketplace-publishing-data-service-creation-odata-mapping.md) att granska definitioner, strukturer och instruktioner.
* Om du är intresserad av att lära sig och förstå de specifika noderna och deras parametrar kan den här artikeln [mappa Data Service OData-noder](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) definitioner och förklaringar, exempel och Använd case kontext.
* Om du vill återgå till den föreskrivna sökvägen för att publicera en datatjänst på Azure Marketplace, den här artikeln [Data Service publiceringsguide](marketplace-publishing-data-service-creation.md).

