---
title: Ausführen von Apache Sqoop-Aufträgen mit Azure HDInsight (Apache Hadoop)
description: Erfahren Sie, wie Sie Azure PowerShell auf einer Arbeitsstation verwenden können, um Sqoop-Importe und -Exporte zwischen einem Hadoop-Cluster und einer Azure SQL-Datenbank auszuführen.
ms.reviewer: jasonh
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 9351ffabd1a263de1ff262fe5f6fef48be518836
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446207"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Verwenden von Apache Sqoop mit Hadoop in HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Apache Sqoop in HDInsight zum Importieren und Exportieren zwischen HDInsight-Cluster und Azure SQL-Datenbank oder SQL Server-Datenbank verwendet werden kann.

Auch wenn Apache Hadoop die beste Wahl für die Verarbeitung unstrukturierter und halbstrukturierter Daten wie z. B. Protokolle und Dateien ist, besteht oft auch Bedarf für die Verarbeitung strukturierter Daten, die in relationalen Datenbanken gespeichert werden.

[Apache Sqoop][sqoop-user-guide-1.4.4] ist ein Tool zum Übertragen von Daten zwischen Hadoop-Clustern und relationalen Datenbanken. Sie können damit Daten aus einem Managementsystem für relationale Datenbanken (RDBMS) wie SQL Server, MySQL oder Oracle in das verteilte Dateisystem von Hadoop (HDFS) importieren, die Daten in Hadoop mit MapReduce oder Apache Hive transformieren und sie anschließend wieder in ein RDBMS exportieren. In diesem Beispiel verwenden Sie eine SQL-Serverdatenbank als relationale Datenbank.

Informationen zu den in HDInsight-Clustern unterstützten Sqoop-Versionen finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen][hdinsight-versions].

## <a name="understand-the-scenario"></a>Das Szenario

Der HDInsight-Cluster wird mit einigen Beispieldaten geliefert. Sie verwenden die beiden folgenden Beispiele:

* Eine Apache-Protokolldatei namens Log4j, die sich unter */example/data/sample.log*befindet. Die folgenden Protokolle werden aus der Datei extrahiert:
  
        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...
* Eine Hive-Tabelle namens *hivesampletable*, die auf die Datendatei unter */hive/warehouse/hivesampletable* verweist. Die Tabelle enthält einige Mobilgerätedaten. 
  
  | Feld | Datentyp |
  | --- | --- |
  | clientid |Zeichenfolge |
  | querytime |Zeichenfolge |
  | market |Zeichenfolge |
  | deviceplatform |Zeichenfolge |
  | devicemake |Zeichenfolge |
  | devicemodel |Zeichenfolge |
  | state |Zeichenfolge |
  | country |Zeichenfolge |
  | querydwelltime |double |
  | sessionid |bigint |
  | sessionpagevieworder |bigint |

In diesem Tutorial verwenden Sie diese beiden Datasets zum Testen des Sqoop-Imports- und -Exports.

## <a name="create-cluster-and-sql-database"></a>Erstellen von Cluster und SQL-Datenbank
In diesem Abschnitt wird gezeigt, wie Sie mit dem Azure-Portal und einer Azure Resource Manager-Vorlage einen Cluster, eine SQL-Datenbank und die SQL-Datenbankschemas erstellen, die Sie zum Ausführen des Tutorials benötigen. Die Vorlage finden Sie in den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Die Resource Manager-Vorlage ruft ein bacpac-Paket auf, um die Tabellenschemas in der SQL-Datenbank bereitzustellen.  Das BACPAC-Paket befindet sich in einem öffentlichen Blobcontainer, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Wenn Sie einen privaten Container für die bacpac-Dateien verwenden möchten, verwenden Sie die folgenden Werte in der Vorlage:
   
```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

Wenn Sie den Cluster und die SQL-Datenbank lieber mit Azure PowerShell erstellen möchten, helfen Ihnen die Informationen in [Anhang A](#appendix-a---a-powershell-sample) weiter.

> [!NOTE]  
> Der Import über eine Vorlage oder das Azure-Portal unterstützt nur das Importieren einer BACPAC-Datei aus Azure Blob Storage.

**So konfigurieren Sie die Umgebung mit einer Ressourcenverwaltungsvorlage**
1. Klicken Sie auf das folgende Bild, um eine Resource Manager-Vorlage im Azure-Portal zu öffnen.         
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
2. Geben Sie die folgenden Eigenschaften ein:

    - **Abonnement**: Geben Sie Ihr Azure-Abonnement ein.
    - **Ressourcengruppe**: Erstellen Sie eine neue Azure-Ressourcengruppe, oder wählen Sie eine vorhandene aus.  Eine Ressourcengruppe dient der Verwaltung.  Sie ist ein Container für Objekte.
    - **Standort**: Wählen Sie eine Region aus.
    - **ClusterName:** Geben Sie einen Namen für den Hadoop-Cluster ein.
    - **Clusteranmeldename und Kennwort:** Der Standardanmeldename lautet „admin“.
    - **SSH-Benutzername und -Kennwort**.
    - **Anmeldename und Kennwort für den SQL-Datenbankserver**.
    - **_artifacts Location** („_artifacts“-Speicherort): Verwenden Sie den Standardwert, sofern Sie nicht eine eigene BACPAC-Datei an einem anderen Speicherort verwenden möchten.
    - **_artifacts Location Sas Token** (SAS-Token für „_artifacts“-Speicherort): Lassen Sie dieses Feld leer.
    - **BACPAC-Dateiname:** Verwenden Sie den Standardwert, sofern Sie nicht eine eigene BACPAC-Datei verwenden möchten.
     
        Die folgenden Werte sind im Abschnitt mit den Variablen hartcodiert:
        
        |NAME|Wert|
        |----|-----|
        | Name des Standard-Speicherkontos | &lt;ClusterName>store |
        | Servername der Azure SQL-Datenbank | &lt;Clustername>dbserver |
        | Azure SQL-Datenbankname | &lt;Clustername>db |
     
3. Aktivieren Sie **Ich stimme den oben genannten Geschäftsbedingungen zu**.
4. Klicken Sie auf **Kaufen**. Daraufhin wird eine neue Kachel mit der Bezeichnung „Bereitstellung für Vorlagenbereitstellung wird gesendet“ angezeigt. Das Erstellen des Clusters und der SQL-Datenbank dauert ca. 20 Minuten.

Wenn Sie die vorhandene Azure SQL-Datenbank oder Microsoft SQL Server verwenden möchten:

* **Azure SQL-Datenbank:** Sie müssen eine Firewall-Regel für den Azure SQL-Datenbankserver konfigurieren, um Zugriff auf Ihre Arbeitsstation zu erlauben. Anweisungen zur Erstellung einer Azure SQL-Datenbank und zur Konfiguration der Firewall erhalten Sie unter [Erste Schritte mit Azure SQL-Datenbank][sqldatabase-get-started]. 
  
  > [!NOTE]  
  > Eine Azure SQL-Datenbank ermöglicht standardmäßig Verbindungen von Azure-Diensten wie Azure HDInsight. Wenn diese Firewalleinstellung deaktiviert ist, müssen Sie sie im Azure-Portal aktivieren. Anweisungen zum Erstellen einer Azure SQL-Datenbank-Instanz und zum Konfigurieren von Firewallregeln finden Sie unter [Erstellen und Konfigurieren von SQL-Datenbanken][sqldatabase-create-configure].

* **SQL Server:** Wenn sich Ihr HDInsight-Cluster im selben virtuellen Netzwerk in Azure wie SQL Server befindet, können Sie mit den hier beschriebenen Schritten Daten in einer SQL Server-Datenbank importieren und exportieren.
  
  > [!NOTE]  
  > HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.

  
  * Informationen zum Erstellen und Konfigurieren eines virtuellen Netzwerks finden Sie unter [Erstellen eines virtuellen Netzwerks im Azure-Portal](../../virtual-network/quick-create-portal.md).
    
    * Wenn Sie SQL Server in Ihrem Datencenter verwenden, müssen Sie das virtuelle Netzwerk entweder als *Standort-zu-Standort* oder als *Punkt-zu-Standort* konfigurieren.
      
      > [!NOTE]  
      > Für virtuelle Netzwerke im **Punkt-zu-Standort**-Modus muss die Anwendung zum Konfigurieren des VPN-Clients auf dem SQL Server ausgeführt werden. Diese Anwendung ist im **Dashboard** der Konfiguration Ihres virtuellen Azure-Netzwerks verfügbar.


    * Wenn Sie SQL Server auf einem virtuellen Azure-Computer verwenden, können Sie eine beliebige Konfiguration für das virtuelle Netzwerk nehmen, sofern sich der virtuelle Computer, auf dem SQL Server läuft, im gleichen virtuellen Netzwerk befindet wie HDInsight.
  * Informationen zum Erstellen eines HDInsight-Clusters in einem virtuellen Netzwerk finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight mithilfe von benutzerdefinierten Optionen](../hdinsight-hadoop-provision-linux-clusters.md)
    
    > [!NOTE]  
    > Der SQL Server muss eine Authentifizierung zulassen. Sie benötigen eine SQL-Serveranmeldung, um die Schritte in diesem Artikel abzuschließen.


**So überprüfen Sie die Konfiguration**

1. Öffnen Sie die Ressourcengruppe im Azure-Portal. Die Gruppe sollte vier Ressourcen enthalten:

    - Cluster
    - Datenbankserver
    - Datenbank
    - Standardspeicherkonto

2. Öffnen Sie die Datenbank in Microsoft SQL Server Management Studio.  Sie sehen, dass zwei Datenbanken bereitgestellt sind:

    ![Sqoop in SQL Management Studio (Azure HDInsight)](./media/hdinsight-use-sqoop/hdinsight-sqoop-sql-management-studio.png)


## <a name="run-sqoop-jobs"></a>Ausführen von Sqoop-Aufträgen
HDInsight kann Sqoop-Aufträge mit verschiedenen Methoden ausführen. Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Methode für Sie geeignet ist. Folgen Sie anschließend dem Link für eine exemplarische Vorgehensweise.

| **Verwenden Sie dies**, wenn Sie Folgendes wünschen: | ... eine **interaktive** Shell | ...**Batchverarbeitung** | ...mit diesem **Clusterbetriebssystem** | ...von diesem **Clientbetriebssystem** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux |Linux, Unix, Mac OS X oder Windows |
| [.NET SDK für Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |? |Linux oder Windows |Windows (vorläufig) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Linux oder Windows |Windows |

## <a name="limitations"></a>Einschränkungen
* Massenexport: Bei Linux-basiertem HDInsight unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder Azure SQL-Datenbank verwendete Sqoop-Connector derzeit keine Masseneinfügungen.
* Batchverarbeitung: Wenn Sie in Linux-basiertem HDInsight zum Ausführen von Einfügevorgängen den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="next-steps"></a>Nächste Schritte
Nun wissen Sie, wie Sqoop verwendet haben. Weitere Informationen finden Sie unter:

* [Verwenden von Apache Hive mit HDInsight](../hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit HDInsight](../hdinsight-use-pig.md)
* [Hochladen von Daten in HDInsight:][hdinsight-upload-data] Andere Methoden zum Hochladen von Daten in HDInsight/Azure Blob Storage.

## <a name="appendix-a---a-powershell-sample"></a>Anhang A – PowerShell-Beispiel

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Im PowerShell-Beispiel werden die folgenden Schritte ausgeführt:

1. Herstellen einer Verbindung zu Azure.
2. Erstellen Sie eine Azure-Ressourcengruppe. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Azure Resource Manager](../../azure-resource-manager/manage-resource-groups-powershell.md)
3. Erstellen Sie einen Azure SQL-Datenbank-Server, eine Azure SQL-Datenbank und zwei Tabellen. 
   
    Wenn Sie stattdessen SQL Server verwenden, verwenden Sie die folgenden Anweisungen, um die Tabellen zu erstellen:
   
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))
   
        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])
   
    Die einfachste Möglichkeit, die Datenbank und Tabellen zu untersuchen, ist mithilfe von Visual Studio. Der Datenbankserver und die Datenbank können mithilfe des Azure-Portals untersucht werden.
4. Erstellen Sie ein HDInsight-Cluster.
   
    Um das Cluster zu untersuchen, können Sie das Azure-Portal oder Azure PowerShell verwenden.
5. Verarbeiten Sie die Quelldatei vorab.
   
    In diesem Tutorial exportieren Sie die Protokolldatei „log4j“ (eine Datei mit Trennzeichen) und eine Hive-Tabelle in eine Azure SQL-Datenbank. Der Name der getrennten Datei lautet */example/data/sample.log*. In diesem Tutorial haben wir Ihnen bereits einige Beispiele für log4j-Protokolle gezeigt. In der Protokolldatei existieren einige Leerzeilen und einige Zeilen, die ungefähr wie folgt aussehen:
   
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
   
    Dies ist kein Problem für andere Beispiele, die diese Daten verwenden. Wir müssen die Ausnahmen jedoch beheben, bevor wir die Daten in die Azure SQL-Datenbank oder in SQL Server importieren können. Der Sqoop-Export schlägt fehl, wenn es eine leere Zeichenfolge oder eine Zeile mit weniger Elementen gibt, als in der Azure SQL-Datenbanktabelle Felder definiert sind. Die log4jlogs-Tabelle hat sieben Felder mit Zeichenfolgen.
   
    Diese Prozedur erstellt eine neue Datei auf dem Cluster: tutorials/usesqoop/data/sample.log. Zur Untersuchung der geänderten Datendatei können Sie das Azure-Portal, ein Azure Storage-Explorer-Tool oder Azure PowerShell verwenden. In [Erste Schritte mit HDInsight][hdinsight-get-started] finden Sie ein Codebeispiel für die Verwendung von Azure PowerShell zum Herunterladen einer Datei und zum Anzeigen von deren Inhalt.
6. Exportieren Sie eine Datendatei in die Azure SQL-Datenbank.
   
    Die Quelldatei ist „tutorials/usesqoop/data/sample.log“. Die Tabelle, in die die Daten exportiert werden, heißt „log4jlogs“.
   
   > [!NOTE]  
   > Mit Ausnahme der Verbindungszeichenfolgen sollten die Schritte in diesem Abschnitt sowohl für Azure SQL-Datenbanken als auch für SQL Server funktionieren. Diese Schritte wurden mithilfe der folgenden Konfiguration getestet:
   > 
   > * **Punkt-zu-Site-Konfiguration für virtuelles Azure-Netzwerk:** Ein virtuelles Netzwerk verbindet den HDInsight-Cluster mit einem SQL Server in einem privaten Rechenzentrum. Weitere Informationen finden Sie unter [Konfigurieren eines Punkt-zu-Standort-VPN im Verwaltungsportal](../../vpn-gateway/vpn-gateway-point-to-site-create.md) .
   > * **Azure HDInsight:** Informationen zum Erstellen eines Clusters in einem virtuellen Netzwerk finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight mit benutzerdefinierten Optionen](../hdinsight-hadoop-provision-linux-clusters.md).
   > * **SQL Server 2014:** Konfiguriert für die Authentifizierung und mit dem Konfigurationspaket für VPN-Clients für eine sichere Verbindung mit dem virtuellen Netzwerk.
   > 
   > 
7. Exportieren Sie eine Hive-Tabelle in die Azure SQL-Datenbank.
8. Importieren Sie die Tabelle "mobiledata" in das HDInsight-Cluster.
   
    Zur Untersuchung der geänderten Datendatei können Sie das Azure-Portal, ein Azure Storage-Explorer-Tool oder Azure PowerShell verwenden.  In [Erste Schritte mit HDInsight][hdinsight-get-started] finden Sie ein Codebeispiel für die Verwendung von Azure PowerShell zum Herunterladen einer Datei und zum Anzeigen von deren Inhalt.

### <a name="the-powershell-sample"></a>Das PowerShell-Beispiel

```powershell
# Prepare an Azure SQL database to be used by the Sqoop tutorial

#region - provide the following values

$subscriptionID = "<Enter your Azure Subscription ID>"

$sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
$sqlDatabasePassword = "<Enter a Password>"

$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh username
$sshPassword = $httpPassword 

# used for creating Azure service names
$nameToken = "<Enter an alias>" 
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
#endregion

#region - variables

# Resource group variables
$resourceGroupName = $namePrefix + "rg"
$location = "East US 2" # used by all Azure services defined in this tutorial

# SQL database varialbes
$sqlDatabaseServerName = $namePrefix + "sqldbserver"
$sqlDatabaseName = $namePrefix + "sqldb"
$sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
$sqlDatabaseMaxSizeGB = 10

# Used for retrieving external IP address and creating firewall rules
$ipAddressRestService = "https://bot.whatismyipaddress.com"
$fireWallRuleName = "UseSqoop"

# Used for creating tables and clustered indexes
$cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
    [t1] [nvarchar](50),
    [t2] [nvarchar](50),
    [t3] [nvarchar](50),
    [t4] [nvarchar](50),
    [t5] [nvarchar](50),
    [t6] [nvarchar](50),
    [t7] [nvarchar](50))"

$cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"

$cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder][bigint])"

$cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"

# HDInsight variables
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}
#endregion

#region - Create Azure resource group
Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
try{
    Get-AzResourceGroup -Name $resourceGroupName
}
catch{
    New-AzResourceGroup -Name $resourceGroupName -Location $location
}
#endregion

#region - Create Azure SQL database server
Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
try{
    Get-AzSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
catch{
    Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green

    $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)

    $sqlDatabaseServerName = (New-AzSqlServer `
                                -ResourceGroupName $resourceGroupName `
                                -ServerName $sqlDatabaseServerName `
                                -SqlAdministratorCredentials $credential `
                                -Location $location).ServerName
    Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan

    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
    New-AzSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-workstation" `
        -StartIpAddress $workstationIPAddress `
        -EndIpAddress $workstationIPAddress

    #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
    #Note that this allows Azure traffic from any Azure subscription to access the server.
    New-AzSqlServerFirewallRule `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -FirewallRuleName "$fireWallRuleName-Azureservices" `
        -StartIpAddress "0.0.0.0" `
        -EndIpAddress "0.0.0.0"
}

#endregion

#region - Create and validate Azure SQL database
Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green

try {
    Get-AzSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName
}
catch {
    Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
    New-AzSqlDatabase `
        -ResourceGroupName $resourceGroupName `
        -ServerName $sqlDatabaseServerName `
        -DatabaseName $sqlDatabaseName `
        -Edition "Standard" `
        -RequestedServiceObjectiveName "S1"
}

#endregion

#region - Create tables
Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green

$conn = New-Object System.Data.SqlClient.SqlConnection
$conn.ConnectionString = $sqlDatabaseConnectionString
$conn.Open()

# Create the log4jlogs table and index
$cmd = New-Object System.Data.SqlClient.SqlCommand
$cmd.Connection = $conn
$cmd.CommandText = $cmdCreateLog4jTable
$ret = $cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateLog4jClusteredIndex
$cmd.ExecuteNonQuery()

# Create the mobiledata table and index
$cmd.CommandText = $cmdCreateMobileTable
$cmd.ExecuteNonQuery()
$cmd.CommandText = $cmdCreateMobileDataClusteredIndex
$cmd.ExecuteNonQuery()

$conn.close()

#endregion


#region - Create HDInsight cluster

Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green

# Create the default storage account
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_LRS

# Create the default Blob container
$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageAccountContext = New-AzStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey 
New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageAccountContext 

# Create the HDInsight cluster
$pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $HDInsightClusterName `
    -Location $location `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version 3.6 `
    -ClusterSizeInNodes 2 `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultStorageContainer $defaultBlobContainerName  

# Validate the cluster
Get-AzHDInsightCluster -ClusterName $hdinsightClusterName
#endregion

#region - pre-process the source file

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

# This procedure creates a new file with $destBlobName
$sourceBlobName = "example/data/sample.log"
$destBlobName = "tutorials/usesqoop/data/sample.log"

# Define the connection string
$storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"

# Create block blob objects referencing the source and destination blob.
$storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
$storageClient = $storageAccount.CreateCloudBlobClient();
$storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
$sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
$destBlob = $storageContainer.GetBlockBlobReference($destBlobName)

# Define a MemoryStream and a StreamReader for reading from the source file
$stream = New-Object System.IO.MemoryStream
$stream = $sourceBlob.OpenRead()
$sReader = New-Object System.IO.StreamReader($stream)

# Define a MemoryStream and a StreamWriter for writing into the destination file
$memStream = New-Object System.IO.MemoryStream
$writeStream = New-Object System.IO.StreamWriter $memStream

# Pre-process the source blob
$exString = "java.lang.Exception:"
while(-Not $sReader.EndOfStream){
    $line = $sReader.ReadLine()
    $split = $line.Split(" ")

    # remove the "java.lang.Exception" from the first element of the array
    # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
    if ($split[0] -eq $exString){
        #create a new ArrayList to remove $split[0]
        $newArray = [System.Collections.ArrayList] $split
        $newArray.Remove($exString)

        # update $split and $line
        $split = $newArray
        $line = $newArray -join(" ")
    }

    # remove the lines that has less than 7 elements
    if ($split.count -ge 7){
        write-host $line
        $writeStream.WriteLine($line)
    }
}

# Write to the destination blob
$writeStream.Flush()
$memStream.Seek(0, "Begin")
$destBlob.UploadFromStream($memStream)

#endregion

#region - export a log file from the cluster to the SQL database

Write-Host "Preprocessing the source file ..." -ForegroundColor Green

$tableName_log4j = "log4jlogs"

# Connection string for Azure SQL Database.
# Comment if using SQL Server
$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
# Connection string for SQL Server.
# Uncomment if using SQL Server.
#$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"

$exportDir_log4j = "/tutorials/usesqoop/data"

# Submit a Sqoop job
$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose
Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput

#endregion

#region - export a Hive table

$tableName_mobile = "mobiledata"
$exportDir_mobile = "/hive/warehouse/hivesampletable"

$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion

#region - import a database

$targetDir_mobile = "/tutorials/usesqoop/importeddata/"

$sqoopDef = New-AzHDInsightSqoopJobDefinition `
    -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"

$sqoopJob = Start-AzHDInsightJob `
                -ClusterName $hdinsightClusterName `
                -HttpCredential $httpCredential `
                -JobDefinition $sqoopDef #-Debug -Verbose

Wait-AzHDInsightJob `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId

Write-Host "Standard Error" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardError

Write-Host "Standard Output" -BackgroundColor Green
Get-AzHDInsightJobOutput `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -DefaultStorageAccountName $defaultStorageAccountName `
    -DefaultStorageAccountKey $defaultStorageAccountKey `
    -DefaultContainer $defaultBlobContainerName `
    -HttpCredential $httpCredential `
    -JobId $sqoopJob.JobId `
    -DisplayOutputType StandardOutput

#endregion
```


[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: ../hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../../sql-database/sql-database-get-started.md
[sqldatabase-create-configure]: ../../sql-database/sql-database-get-started.md

[powershell-start]: https://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
