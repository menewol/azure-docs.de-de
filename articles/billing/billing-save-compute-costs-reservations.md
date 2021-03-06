---
title: Was sind Azure-Reservierungen? | Microsoft-Dokumentation
description: Erfahren Sie mehr über Azure-Reservierungen und -Preise, um Kosten für virtuelle Computer, SQL-Datenbank-Instanzen, Azure Cosmos DB-Instanzen und andere Ressourcen zu sparen.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: 1349a05e1dd235c7b375335ae2c9fed16170a61f
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649389"
---
# <a name="what-are-azure-reservations"></a>Was sind Azure-Reservierungen?

Mit Azure-Reservierungen können Sie Geld sparen, indem Sie für virtuelle Computer, SQL-Datenbank-Computekapazität, Azure Cosmos DB-Durchsatz oder andere Azure-Ressourcen für ein Jahr oder für drei Jahre im Voraus zahlen. Durch die Vorabzahlung können Sie einen Rabatt für die Ressourcen, die Sie nutzen, in Anspruch nehmen. Reservierungen können Ihre Kosten für virtuelle Computer, SQL-Datenbank-Compute, Azure Cosmos DB-Kapazitäten oder andere Ressourcen deutlich um bis zu 72 % gegenüber der nutzungsbasierten Bezahlung reduzieren. Reservierungen bieten einen Abrechnungsrabatt und wirken sich nicht auf den Laufzeitstatus Ihrer Ressourcen aus.

Sie können eine Reservierung im [Azure-Portal](https://aka.ms/reservations) erwerben. Weitere Informationen finden Sie in den folgenden Artikeln:

Servicepläne:
- [Virtuelle Computer mit Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure Cosmos DB-Ressourcen mit reservierter Azure Cosmos DB-Kapazität](../cosmos-db/cosmos-db-reserved-capacity.md)
- [SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../sql-database/sql-database-reserved-capacity.md)

Softwarepläne:
- [Red Hat-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [SUSE-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-suse-software-charges.md)

## <a name="why-buy-a-reservation"></a>Warum eine Reservierung kaufen?

Wenn Sie über virtuelle Computer, Azure Cosmos DB- oder SQL-Datenbank-Instanzen verfügen, die für längere Zeiträume ausgeführt werden, erhalten Sie durch den Kauf einer reservierten Instanz die kostengünstigste Option. Wenn Sie beispielsweise kontinuierlich vier Instanzen von einem Dienst ohne Reservierung ausführen, fallen die Gebühren für die nutzungsbasierte Bezahlung an. Wenn Sie eine Reservierung für diese Ressourcen kaufen, erhalten Sie sofort den Rabatt auf Reservierungen. Die Ressourcen werden nicht mehr mit den Gebühren für die nutzungsbasierte Bezahlung in Rechnung gestellt.

## <a name="charges-covered-by-reservation"></a>Gebühren, die durch Reservierung abgedeckt werden

Servicepläne:

- Reservierte VM-Instanz: Eine Reservierung deckt nur die VM-Computekosten ab. Eine Reservierung deckt keine zusätzlichen Kosten für Software, Netzwerke oder Speicher ab.
- Reservierte Azure Cosmos DB-Kapazität: Eine Reservierung deckt den für Ihre Ressourcen bereitgestellten Durchsatz ab. Die Speicher- und Netzwerkkosten werden nicht abgedeckt.
- Für SQL-Datenbank reservierter virtueller Kern: In einer Reservierung sind nur die Computekosten enthalten. Die Lizenz wird separat abgerechnet.
- Reservierte Azure Cosmos DB-Kapazität: Eine Reservierung deckt den für Ihre Ressourcen bereitgestellten Durchsatz ab, nicht die Speicher- und Netzwerkkosten.

Für virtuelle Windows-Computer und SQL-Datenbank können Sie die Lizenzierungskosten mit dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) decken.

## <a name="whos-eligible-to-purchase-a-reservation"></a>Wer ist zum Erwerb einer Reservierung berechtigt?

Um einen Plan zu kaufen, müssen Sie eine Rolle des Abonnementverantwortlichen bei einem Enterprise-Abonnement (MS-AZR-0017P oder MS-AZR-0148P) oder einem Abonnement mit nutzungsbasierter Zahlung (MS-AZR-003P oder MS-AZR-0023P) haben. Cloudlösungsanbieter können Azure-Reservierungen über das Azure-Portal oder  [Partner Center](/partner-center/azure-reservations)  erwerben.

EA-Kunden können Käufe für EA-Administratoren beschränken, indem sie die Option **Reservierte Instanzen hinzufügen** in EA-Portal deaktivieren. EA-Administratoren müssen der Besitzer für mindestens ein EA-Abonnement sein, um eine Reservierung zu erwerben. Die Option ist nützlich für Unternehmen, die ein zentralisiertes Team benötigen, um Reservierungen für verschiedene Kostenstellen einzukaufen. Nach dem Kauf können zentralisierte Teams die Reservierungen um Kostenstellenbesitzer erweitern. Die Besitzer können dann die Reservierung auf ihre Abonnements ausdehnen. Das zentrale Team muss keinen Zugriff des Abonnenten haben, wenn die Reservierung gekauft wird.

Der Rabatt auf Reservierungen gilt nur für Ressourcen, denen einer der Abonnementtypen „Enterprise“, „Nutzungsbasierte Bezahlung“ oder „CSP“ zugeordnet ist.

## <a name="how-is-a-reservation-billed"></a>Wie wird eine Reservierung abgerechnet?

Die Reservierung wird mit der Zahlungsmethode in Rechnung gestellt, die mit dem Abonnement verknüpft ist. Wenn Sie über ein Enterprise-Abonnement verfügen, werden die Reservierungskosten von Ihrem Verpflichtungsguthaben abgezogen. Wenn Ihr Zahlungsverpflichtungssaldo die Kosten für die Reservierung nicht abdeckt, wird Ihnen die Überschreitung in Rechnung gestellt. Wenn Sie über ein Abonnement mit nutzungsbasierter Bezahlung verfügen, wird die Kreditkarte für Ihr Konto umgehend belastet. Wenn Sie Rechnungen erhalten, sind die Gebühren Ihrer nächsten Rechnung aufgeführt.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Der Reservierungsrabatt gilt für die Ressourcennutzung, die den Attribute entspricht, die Sie beim Erwerb der Reservierung auswählen. Zu diesen Attributen gehört der Bereich, in dem die entsprechenden VMs, SQL-Datenbank-Instanzen, Azure Cosmos DB-Instanzen oder anderen Ressourcen ausgeführt werden. Beispiel: Wenn Sie einen Rabatt auf eine Reservierung für vier virtuelle Computer des Typs „Standard D2“ in der Region „USA, Westen“ nutzen möchten, wählen Sie das Abonnement aus, in dem die VMs ausgeführt werden. Wenn die virtuellen Computer in verschiedenen Abonnements in Ihrer Registrierung/Ihrem Konto ausgeführt werden, wählen Sie den Bereich „Freigegeben“ aus. Der Bereich „Freigegeben“ ermöglicht die abonnementübergreifende Anwendung des Reservierungsrabatts. Sie können den Bereich nach dem Erwerb einer Reservierung ändern. Weitere Informationen finden Sie unter [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md).

Der Rabatt auf Reservierungen gilt nur für Ressourcen, denen einer der Abonnementtypen „Enterprise“, „Nutzungsbasierte Bezahlung“ oder „CSP“ zugeordnet ist. Für Ressourcen, die in einem Abonnement mit anderen Angebotstypen ausgeführt werden, gilt der Reservierungsrabatt nicht.

Um besser zu verstehen, wie sich Reservierungen auf Ihre Abrechnung auswirken, lesen Sie die folgenden Artikel:

Servicepläne:

- [Grundlegendes zum Rabatt für reservierte Azure-VM-Instanzen](billing-understand-vm-reservation-charges.md)
- [Grundlegendes zum Rabatt für Azure-Reservierungen](billing-understand-vm-reservation-charges.md)
- [Understand Azure Cosmos DB reservation discount (Grundlegendes zum Rabatt für Azure Cosmos DB-Reservierungen)](billing-understand-cosmosdb-reservation-charges.md)

Softwarepläne:

- [Grundlegendes zum Rabatt für Red Hat und zur Verwendung von Azure-Reservierungen unter Red Hat](billing-understand-rhel-reservation-charges.md)
- [Grundlegendes zur Anwendung des Rabatts für den SUSE Linux Enterprise-Softwareplan](billing-understand-suse-reservation-charges.md)

## <a name="when-the-reservation-term-expires"></a>Wann das Ende des Reservierungszeitraums erreicht ist

Am Ende der Laufzeit der Reservierung läuft der Abrechnungsrabatt ab, und die Infrastruktur der virtuellen Computer, SQL-Datenbank-Instanzen, Azure Cosmos DB-Instanzen oder anderen Ressourcen wird mit den Gebühren für die nutzungsbasierte Bezahlung in Rechnung gestellt. Azure-Reservierungen verlängern sich nicht automatisch. Zur weiteren Nutzung des Abrechnungsrabatts müssen Sie eine neue Reservierung für Dienste und Software erwerben, die für Reservierungen berechtigt sind.

## <a name="discount-applies-to-different-sizes"></a>Rabatt gilt für verschiedene Größen

Wenn Sie eine Reservierung erwerben, kann der Rabatt auf andere Instanzen mit Attributen angewendet werden, die sich in der gleichen Größengruppe befinden. Dieses Feature wird als „Instanzgrößenflexibilität“ bezeichnet. Die Flexibilität der Rabattabdeckung hängt vom Reservierungstyp und den Attributen ab, die Sie beim Erwerb der Reservierung auswählen.

Servicepläne:

- Reservierte VM-Instanzen: Wenn Sie die Reservierung erwerben und unter **Optimiert für** die Option **Instanzgrößenflexibilität** auswählen, hängt die Abdeckung des Rabatts von der ausgewählten VM-Größe ab. Die Reservierung kann für VM-Größen in derselben Größenordnung gelten. Weitere Informationen finden Sie unter [Flexibilität bei der VM-Größe mit reservierten VM-Instanzen](../virtual-machines/windows/reserved-vm-instance-size-flexibility.md).
- Reservierte SQL-Datenbank-Kapazität: Die Rabattabdeckung hängt von der ausgewählten Leistungsstufe ab. Weitere Informationen finden Sie unter [Grundlegendes zur Anwendung eines Rabatts für Azure-Reservierungen auf SQL-Datenbank-Instanzen](billing-understand-reservation-charges.md).
- Reservierte Azure Cosmos DB-Kapazität: Die Rabattabdeckung hängt vom bereitgestellten Durchsatz ab. Weitere Informationen finden Sie unter [Understand how an Azure Cosmos DB reservation discount is applied (Grundlegendes zur Anwendung eines Rabatts für Azure Cosmos DB-Reservierungen)](billing-understand-cosmosdb-reservation-charges.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Sparen Sie durch den Erwerb einer [reservierten VM-Instanz](../virtual-machines/windows/prepay-reserved-vm-instances.md), einer [reservierten SQL-Datenbank-Kapazität](../sql-database/sql-database-reserved-capacity.md) oder einer [reservierten Azure Cosmos DB-Kapazität](../cosmos-db/cosmos-db-reserved-capacity.md) bei den Kosten für Ihre virtuellen Computer.
- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
    - [Verwalten von Azure-Reservierungen](billing-manage-reserved-vm-instance.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
    - [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](billing-reserved-instance-windows-software-costs.md)
    - [Verkaufen Microsoft Azure Reserved Instances](https://docs.microsoft.com/partner-center/azure-reservations)
