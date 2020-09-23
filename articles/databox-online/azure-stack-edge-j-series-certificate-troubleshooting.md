---
title: Certifikat fel sökning med Azure Stack Edge Pro med GPU | Microsoft Docs
description: Beskriver fel sökning av certifikat fel med Azure Stack Edge Pro GPU-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: a0918c6cebd50231a9664811bb467e04d2d2bfd9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90891337"
---
# <a name="troubleshooting-certificate-errors"></a>Felsöka certifikat fel

Artikeln innehåller fel sökning av vanliga certifikat fel när du installerar certifikat till din Azure Stack Edge Pro-enhet.

## <a name="common-certificate-errors"></a>Vanliga certifikat fel

Följande tabell visar vanliga certifikat fel och detaljerad information om dessa fel och möjliga lösningar:

> [!NOTE]
> Förekomster av &#8220;{0} , {1} ,..., {n} &#8221; anger positions parametrar. Positions parametrarna tar värden beroende på de certifikat som du använder.

| Felkod | Beskrivning |
|---|---|
| CertificateManagement_UntrustedCertificate | Certifikatet med ämnes namnet {0} har en certifikat kedja bruten. Ladda upp certifikatet för signerings kedjan innan du laddar upp det här certifikatet.|
| CertificateManagement_DeviceNotRegistered| Enheten är inte aktive rad. Du kan bara överföra ett support certifikat efter aktiveringen.|
| CertificateManagement_EmptySAN | Certifikatet med ämnes namnet {0} har inget alternativt mottagar namn. Kontrol lera certifikat egenskaperna och ta med ett nytt certifikat.|
| CertificateManagement_ExpiredCertificate | Certifikatet med typen {0} har upphört att gälla eller går ut snart. Kontrol lera förfallo datum för certifikatet och ta i ett nytt certifikat vid behov.|
| CertificateManagement_FormatMismatch | Certifikat formatet stöds inte. Kontrol lera certifikat formatet och hämta ett nytt certifikat vid behov.  Förväntad {0} , hittades {1} . |
| CertificateManagement_GenericError | Det gick inte att utföra certifikat hanterings åtgärden. Försök igen om några minuter. Kontakta Microsoft Support om problemet kvarstår. |
| CertificateManagement_HttpsBindingFailure | Certifikatet med ämnes namnet {0} kunde inte skapa en säker https-kanal. Kontrol lera certifikatet som du har laddat upp och ta ett nytt certifikat om det behövs. Det här felet uppstår med enhetens Node-certifikat.|
| CertificateManagement_IncorrectKeyCertSignKeyUsage | Certifikatet med ämnes namnet {0} får inte ha certifikat signering för nyckel användning. Kontrol lera nyckel användningen för certifikatet och ta med ett nytt certifikat vid behov. Det här felet uppstår med signerings kedjans certifikat. |
| CertificateManagement_IncorrectKeyNumber | Certifikatet med ämnes namnet {0} har ett felaktigt nyckel nummer {1} . Kontrol lera nyckel numret för certifikatet och ta med ett nytt certifikat vid behov.|
| CertificateManagement_InvalidP7b | Den uppladdade certifikat filen är inte giltig.  Kontrol lera certifikat formatet och hämta ett nytt certifikat vid behov.|
| CertificateManagement_KeyAlgorithmNotRSA | Det här certifikatet använder inte RSA-nyckelalgoritm. Endast RSA-certifikat stöds. |
| CertificateManagement_KeySizeNotSufficient | Certifikatet med ämnes namnet {0} har otillräcklig nyckel storlek {1} . Den minsta nyckel storleken är 4096.|
| CertificateManagement_MissingClientOid | Certifikatet med ämnes namnet saknar {0} OID för klientautentisering. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat.|
| CertificateManagement_MissingDigitalSignatureKeyUsage | Certifikatet med ämnes namnet saknar {0} digital signatur i nyckel användningen. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat. |
| CertificateManagement_MissingEntryInSAN | Certifikatet med ämnes namnet {0} har inte någon ämnes namns post i det alternativa certifikat mottagar namnet. Kontrol lera certifikat egenskaperna och ta med ett nytt certifikat. |
| CertificateManagement_MissingKeyCertSignKeyUsage | Certifikatet med ämnes namnet saknar {0} certifikat signering i nyckel användningen. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat.|
| CertificateManagement_MissingKeyEnciphermentKeyUsage | Certifikatet med ämnes namnet {0} har ingen nyckel kryptering i nyckel användningen. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat. |
| CertificateManagement_MissingServerOid | Certifikatet med ämnes namnet saknar {0} OID för serverautentisering. Kontrol lera certifikatets egenskaper och om det behövs kan du ta med ett nytt certifikat.|
| CertificateManagement_NameMismatch | Felaktig matchning av certifikat typ. Förväntat omfång: {0} , hittades {1} . Ladda upp lämpligt certifikat.|
| CertificateManagement_NoPrivateKeyPresent | {0}Det finns ingen privat nyckel för certifikatet med ämnes namnet. Ladda upp ett. PFX-certifikat med privat nyckel.|
| CertificateManagement_NotSelfSignedCertificate | Certifikatet med ämnes namnet {0} är inte självsignerat. Rot certifikaten ska vara självsignerade |
| CertificateManagement_NotSupportedOnVirtualAppliance | Den här åtgärden stöds inte på den virtuella enheten. Det här felet indikerar att signering endast sker med Data Box Gateway som körs i moln installationen i taktiska. Felet uppstår när enheten hanteras via Windows PowerShell.|
| CertificateManagement_SelfSignedCertificate | Certifikatet med ämnes namnet {0} är självsignerat. Ladda upp ett certifikat som är korrekt signerat.|
| CertificateManagement_SignatureAlgorithmSha1 | Certifikatet med ämnes namnet {0} innehåller en signaturalgoritm som inte stöds. SHA1-RSA stöds inte. |
| CertificateManagement_SubjectNamesInvalid | Certifikatet med ämnes namnet {0} har inte rätt ämnes namn eller alternativa namn för {1} certifikat mottagare. Kontrol lera certifikatet som du har laddat upp och ta ett nytt certifikat om det behövs. Du bör också kontrol lera DNS-namnet för att matcha San-namnen.|
| CertificateManagement_UnreadableCertificate | {0}Det gick inte att läsa certifikatet med typen. Felet uppstår när certifikatet är oläsligt eller skadat. Hämta ett nytt certifikat.|
| CertificateSubjectNotFound | {0}Det gick inte att hitta certifikatet med ämnes namnet. Hämta ett nytt certifikat.|

## <a name="next-steps"></a>Nästa steg

[Certifikat krav](azure-stack-edge-j-series-certificate-requirements.md)