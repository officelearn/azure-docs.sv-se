---
title: Verktyg för att använda Ansible med Azure
description: Installera och använda verktygen för Ansible med Azure
ms.service: ansible
keywords: ansible, azure, devops, verktyg, vs-kod, visual studio-koden, tillägg
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: 73d31054bb0c40dd08bf4b4a93c31c59354b5bed
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
ms.locfileid: "28212052"
---
# <a name="tools-for-using-ansible-with-azure"></a>Verktyg för att använda Ansible med Azure

Följande verktyg gör arbetet med Ansible och Azure enklare och effektivare.

## <a name="visual-studio-code-extension-for-ansible"></a>Visual Studio Code-tillägget för Ansible

Den [Visual Studio Code-tillägget för Ansible](https://marketplace.visualstudio.com/items?itemName=vscoss.vscode-ansible) innehåller flera funktioner för att använda Ansible från Visual Studio-koden:

- Autokomplettering Ansible direktiven, moduler och plugin-program i Ansible-dokumentationen som du skriver.
- Code kodavsnitt visas när du klickar på &lt;Ctrl >&lt;utrymme > när du redigerar Ansible-playbooks.
- Syntaxmarkering visar Ansible playbook koden i olika färger och teckensnitt i enlighet med YAML-syntax.
- Ansible playbooks kan köras från fönstret Visual Studio Code Terminal.
    - (Endast Windows) Ansible kan köras från en dockerbehållare.
    - (Linux och macOS) Ansible kan köras från en dockerbehållare eller från en lokal Ansible installation. 
- Ansible playbooks kan köras från Azure Cloud Shell.