---
title: Enklaven-medvetna behållare i Azure
description: enklaven-stöd för färdiga program behållare på Azure Kubernetes service (AKS)
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: b388b8efb7d993baa243975dbf499576246f3185
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "91001072"
---
# <a name="enclave-aware-containers"></a>Enklaven-medvetna behållare

En enklaven är en skyddad minnes region som ger konfidentialitet för data och kod körning. Det är en instans av en TEE (Trusted Execution Environment) som skyddas av maskin vara. I AKS används [Intel Software Guard-tillägg (SGX)](https://software.intel.com/sgx) för att skapa isolerade enklaven-miljöer i noderna mellan varje behållar program.

Precis som Intel SGX Virtual Machines har behållar program som har utvecklats för att köras i enclaves två komponenter:

- En icke-betrodd komponent (kallas värden) och
- En betrodd komponent (kallas enklaven).

![Arkitektur för enklaven medveten container](./media/enclave-aware-containers/enclaveawarecontainer.png)

Enklaven medveten containers-programarkitektur ger dig den mest kontroll över implementeringen och behåller koden i enklaven-låg. Att minimera koden som körs i enklaven bidrar till att minska risken för attack ytan.   

## <a name="enablers"></a>Aktiverarna

### <a name="open-enclave-sdk"></a>Öppna Enclave SDK
Open enklaven SDK är ett oberoende bibliotek med öppen källkod för utveckling av C-program som använder maskinvarubaserade betrodda körnings miljöer. Den aktuella implementeringen ger stöd för Intel SGX och för hands versions stöd för [op-tee OS på arm-TrustZone](https://optee.readthedocs.io/en/latest/general/about.html).

Kom igång med Open enklaven-baserad container Application [här](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)

### <a name="intel-sgx-sdk"></a>Intel SGX SDK
Intel upprätthåller Software Development Kit för att skapa SGX-program för både Linux-och Windows container-arbetsbelastningar. Windows-behållare stöds för närvarande inte av AKS konfidentiella data behandlings noder.

Kom igång med Intel SGX-baserade program [här](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)

### <a name="confidential-consortium-framework-ccf"></a>CCF (konfidentiellt konsortium Framework)
CCF (konfidentiellt Consortium Framework) är ett ramverk med öppen källkod för att skapa en ny kategori med säkra, hög tillgängliga och utförda program som fokuserar på data behandling och data i flera parter. CCF kan möjliggöra storskaliga, konfidentiella nätverk som uppfyller viktiga företags krav, vilket ger möjlighet att påskynda produktion och företags införande av konsortier baserade blockchain och beräknings teknik för flera parter.

Kom igång med Azures konfidentiella data behandling och CCF [här](https://github.com/Microsoft/CCF)

### <a name="confidential-inferencing-onnx-runtime"></a>Konfidentiell Inferencing ONNX-körning

Öppen källkod enklaven-baserad ONNX-körning upprättar en säker kanal mellan klienten och härlednings tjänsten – se till att varken begäran eller svaret kan lämna den säkra enklaven. 

Med den här lösningen kan du överföra befintlig ML-tränad modell och köra dem konfidentiellt samtidigt som du ger förtroende mellan klienten och servern genom attestering och verifieringar. 

Kom igång med ML modell hiss och växla till ONNX runtime [här](https://aka.ms/confidentialinference)

## <a name="container-samples-implementations"></a>Implementeringar av container exempel

[Azure-exempel för enklaven-medvetna behållare på AKS](https://github.com/Azure-Samples/enclave-aware-container-samples)

<!-- LINKS - external -->
[Azure Attestation]: https://docs.microsoft.com/en-us/azure/attestation/


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
[Confidential Containers]: /confidential-computing/containercompute/confidential-containers