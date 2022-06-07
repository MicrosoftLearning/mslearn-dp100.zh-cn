---
lab:
  title: 检测和缓解不公平性
ms.openlocfilehash: 5e04b41a984fa65b09d3d0a7f249641916438415
ms.sourcegitcommit: 18f734eeb1031a9cb69c3b294632efd2e69324ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2021
ms.locfileid: "137894554"
---
# <a name="detect-and-mitigate-unfairness"></a>检测和缓解不公平性

机器学习模型通常可以封装导致不公平性的无意偏差。 例如，预测患者是否应进行糖尿病检测的机器学习模型可能在部分年龄段的预测准确率会高于其他年龄段，结果就是一部分患者没有得到适当的预防性健康检查或者受到了不必要的临床检查。

## <a name="before-you-start"></a>准备工作

如果尚未完成 *[创建 Azure 机器学习工作区](01-create-a-workspace.md)* 练习以创建 Azure 机器学习工作区和计算实例，请完成它，并克隆本练习所需的笔记本。

## <a name="open-jupyter"></a>打开 Jupyter

虽然可以使用 Azure 机器学习工作室中的“笔记本”页面来运行笔记本，但使用功能齐全的笔记本开发环境（如 Jupyter）通常效率更高。

1. 在 [Azure 机器学习工作室](https://ml.azure.com)中，查看工作区的“计算”页面，并在“计算实例”选项卡上启动计算实例（如果尚未运行） 。
2. 当计算实例正在运行时，单击 Jupyter 链接以在新的浏览器选项卡中打开 Jupyter 主页。

## <a name="use-fairlearn-and-azure-machine-learning-to-detect-unfairness"></a>使用 Fairlearn 和 Azure 机器学习来检测不公平性

本练习在笔记本中提供了用于评估模型不公平性的代码。

1. 在 Jupyter 主页中，浏览到克隆的笔记本存储库所在的 /users/your-user-name/mslearn-dp100 文件夹，然后打开“检测不公平性”笔记本 。
2. 然后阅读笔记本中的笔记，依次运行每个代码单元。
3. 笔记本中的代码运行完毕后，在“文件”菜单上单击“关闭并停止”以关闭它及其 Python 内核 。 然后关闭所有 Jupyter 浏览器选项卡。

## <a name="clean-up"></a>清理

如果你现在完成了 Azure 机器学习的工作，请在 Azure 机器学习工作室的“计算”页上的“计算实例”选项卡上，选择你的计算实例，然后单击“停止”以将其关闭  。 否则，让它继续运行以便你在下一个实验室中使用。

> **注意**：停止计算可确保不会向你的订阅收取计算资源的费用。 但是，只要订阅中存在 Azure 机器学习工作区，就会向你收取少量数据存储费用。 如果已完成对 Azure 机器学习的探索，可以删除 Azure 机器学习工作区和关联的资源。 但是，如果计划完成本系列中的任何其他实验室，则需要先重复 *[创建 Azure 机器学习工作区](01-create-a-workspace.md)* 练习来创建工作区并准备环境。