---
title: "Generera Azure Stack infrastruktur för offentliga nycklar för distribution av Azure-stacken integrerat system | Microsoft Docs"
description: Beskriver Azure Stack PKI-certifikat distribution processfor Azure Stack integrerad-system.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: ppacent
ms.openlocfilehash: a9f2a882947e07cde0e0505458608f86043b2a67
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Generera PKI-certifikat för distribution av Azure-stacken
Nu när du vet [PKI-certifikatkrav](azure-stack-pki-certs.md) för Azure-stacken distributioner måste du hämta dessa certifikat från den certifikatutfärdare (CA) du väljer. 

## <a name="request-certificates-using-an-inf-file"></a>Begär certifikat med hjälp av en INF-fil
Ett sätt att begära certifikat från en offentlig Certifikatutfärdare eller en intern Certifikatutfärdare är med hjälp av en INF-fil. Inbyggda certreq.exe för Windows kan använda en INF-fil som anger certifikat information för att generera en begärandefil som beskrivs i det här avsnittet. 

### <a name="sample-inf-file"></a>Exempel INF-fil 
Exempel certifikatet begäran INF-filen kan användas för att skapa en fil med offline certifikatbegäran för överföring till en CA (intern eller offentlig). INF-filen innehåller alla nödvändiga slutpunkter (inklusive de valfria PaaS-tjänsterna) i ett enda jokerteckencertifikat. 

Inf-exempelfilen förutsätter att regionen är lika med **sea** och externa FQDN-värdet är **Sö &#46;contoso &#46; com**. Ändra dessa värden för att matcha din miljö innan du genererar en. INF-fil för din distribution. 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>Generera och skicka begäran till Certifikatutfärdaren
Följande arbetsflöde beskrivs hur du kan anpassa och använda exempel INF-fil som skapats tidigare för att begära ett certifikat från en Certifikatutfärdare:

1. **Redigera och spara INF-fil**. Kopiera exemplet tillhandahålls och spara den till en ny textfil. Ersätta ämnesnamn och externa FQDN med värden som matchar din distribution och spara filen som en. INF-fil.
2. **Skapa en begäran utifrån certreq**. Med hjälp av en Windows-dator, starta en kommandotolk som administratör och kör följande kommando för att generera en (.req)-begärandefil: `certreq -new <yourinffile>.inf <yourreqfilename>.req`.
3. **Skicka till CA**. Skicka den. REQ filen genereras till Certifikatutfärdaren (kan vara intern eller offentlig).
4. **Importera. CER**. CA: N returnerar en. CER-fil. Använder samma Windows-dator som du genererade förfrågningsfilen, importera den. CER-fil som returneras till datorn/personal-arkivet. 
5. **Exportera och kopiera. PFX till distributionsmappar**. Exportera certifikatet (inklusive den privata nyckeln) som en. PFX-filen och kopiera den. PFX-fil som distributionsmappar som beskrivs i [Azure Stack PKI distributionskrav](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Nästa steg
[Identitetsintegrering](azure-stack-integrate-identity.md)