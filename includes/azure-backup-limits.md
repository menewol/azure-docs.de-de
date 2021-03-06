---
title: Includedatei
description: Includedatei
services: backup
author: rayne-wiselman
ms.service: backup
ms.topic: include
ms.date: 12/07/2018
ms.author: raynew
ms.custom: include file
ms.openlocfilehash: 9e9c09c1825f5c8383a708e8bd343146396f878e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553682"
---
Für Azure Backup gelten die folgenden Beschränkungen.

| **Begrenzung** | **Standard** |
| --- | --- |
| Server oder Computer, die in einem Tresor registriert werden können. | Windows Server/Windows Client/System Center Data Protection Manager: 50 verwendet. <br/><br/> IaaS-VMs: 1.000.  |
| Die Größe einer Datenquelle im Tresorspeicher. |54.400GB Maximum. Die Beschränkung gilt nicht für IaaS-VM-Sicherungen. |
| Sicherungstresore in einem Azure-Abonnement. |500 Tresore pro Region. |
| Planen täglicher Sicherungen. |Windows Server/Client: Drei pro Tag.<br/> System Center DPM Zwei pro Tag. <br/> IaaS-VMs: Eine pro Tag.  |
| Datenträger, die für die Sicherung an einen virtuellen Azure-Computer angeschlossen sind. | 16 |
| Einzelner Datenträger, der für die Sicherung an einen virtuellen Azure-Computer angeschlossen ist.| 4.095GB|
