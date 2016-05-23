<properties
	pageTitle="Azure Active Directory 中的专用组 | Microsoft Azure"
	description="概述专用组在 Azure Active Directory 中的工作原理及其创建方法。"
	services="active-directory"
	documentationCenter=""
	authors="curtand"
	manager="stevenpo"
	editor=""
	/>

<tags 
	ms.service="active-directory" 
	ms.date="03/17/2016"
	wacn.date=""/>

# Azure Active Directory 中的专用组

在 Azure Active Directory (Azure AD) 中，专门的组功能可自动创建并填充 Azure AD 预定义的组的成员身份。无法使用 Azure 经典门户、Windows PowerShell cmdlet 或以编程方式添加或删除专用组的成员。

>[AZURE.NOTE] 专用组要求向以下人员分配 Azure AD Premium 许可证：
>- 管理组规则的管理员，
>- 以及由规则选定为组成员的所有用户。

**启用专用组**

1. 在 [Azure 经典门户](https://manage.windowsazure.com)中，选择“Active Directory”，然后打开你的组织的目录。

2. 选择“组”选项卡，然后打开要编辑的组。

3. 选择“配置”选项卡，然后将“启用专用组”设置为“是”。

一旦将“启用专用组”开关设置为“是”，你就可以通过将“启用‘所有用户’组”开关设置为“是”，来进一步使目录自动创建“所有用户”专用组。然后，你还可以编辑此专用组的名称，方法是在“‘所有用户’组的显示名称”字段中键入该名称。

“所有用户”组可用于向目录中的所有用户分配相同的权限。例如，可以通过向“所有用户”专用组分配对某个 SaaS 应用程序的访问权限，来授予目录中所有用户对该应用程序的访问权限。

“所有用户”专用组包含目录中的所有用户，当中有来宾和外部用户。如果需要排除外部用户的组，则可以使用类似下面的基于属性的动态规则来创建组：

				(user.userPrincipalName -notContains "#EXT#@")

对于排除所有来宾用户的组，可使用和下面类似的规则来创建：

				(user.userType -ne "Guest")

要了解如何为动态组成员身份创建高级规则（包含多个比较条件的规则），请参阅[使用属性创建高级规则](/documentation/articles/active-directory-accessmanagement-groups-with-advanced-rules)。


这些文章提供了有关 Azure Active Directory 的更多信息。

* [使用 Azure Active Directory 组管理对资源的访问](/documentation/articles/active-directory-manage-groups)
* [有关 Azure Active Directory 中应用程序管理的文章索引](active-directory-apps-index.md)
* [什么是 Azure Active Directory？](/documentation/articles/active-directory-whatis)

* [将本地标识与 Azure Active Directory 集成](/documentation/articles/active-directory-aadconnect)

<!---HONumber=Mooncake_0509_2016-->