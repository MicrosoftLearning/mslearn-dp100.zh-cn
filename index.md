---
title: 联机托管说明
permalink: index.html
layout: home
ms.openlocfilehash: a2eb157b1d188655f4cfbcc575befec4a2e9c623
ms.sourcegitcommit: 18f734eeb1031a9cb69c3b294632efd2e69324ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2021
ms.locfileid: "137894571"
---
# <a name="azure-machine-learning-exercises"></a>Azure 机器学习练习

此存储库包含针对 Microsoft 课程 [DP-100 在 Azure 上设计和实现数据科学解决方案](https://docs.microsoft.com/learn/certifications/courses/dp-100t01)的动手实验室练习以及等效的 [Microsoft Learn 上的自定进度模块](https://docs.microsoft.com/learn/paths/build-ai-solutions-with-azure-ml-service/)。 这些练习配合教材提供，你可以使用其描述的方法进行练习。

若要完成这些练习，需要一个 Microsoft Azure 订阅。 如果讲师未提供，可以在 [https://azure.microsoft.com](https://azure.microsoft.com) 注册免费试用版。

{% assign labs = site.pages | where_exp:"page", "page.url contains '/instructions'" %}
| 练习 |
| ------- | 
{% for activity in labs  %}| [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
