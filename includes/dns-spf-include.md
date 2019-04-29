---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563372"
---
Avsändaren princip framework (SPF) poster används för att ange vilka e-postservrar kan skicka e-post åt ett domännamn. SPF-poster är korrekt konfigurerat är viktigt att förhindra att mottagaren Markera din e-postadress som skräppost.

DNS-RFC: er som ursprungligen presenterades en ny SPF-posttyp för att stödja det här scenariot. För äldre DNS-servrar måste tillåtna de också användningen av TXT-posttypen för att ange SPF-poster. Denna tvetydighet ledde till förvirring som löstes genom [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Det säger att SPF-poster måste skapas med hjälp av TXT-posttypen. Den anger också att SPF-posttyp är inaktuell.

**SPF-poster som stöds av Azure DNS och måste skapas med hjälp av TXT-posttypen.** Föråldrad SPF-posttypen stöds inte. När du [importera en DNS-zonfil](../articles/dns/dns-import-export.md), SPF-poster som använder posttypen SPF konverteras till TXT-posttypen.
