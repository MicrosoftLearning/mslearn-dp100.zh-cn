---
lab:
  title: 使用自动化机器学习
ms.openlocfilehash: 9836a169752705779f263e7b005baf11e2f7b616
ms.sourcegitcommit: 8601551af6c32a4c75fd9ecffce750583c2ab4b8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2022
ms.locfileid: "141346683"
---
# <a name="use-automated-machine-learning"></a>使用自动化机器学习

Azure 机器学习包括一种自动化机器学习功能，该功能利用云计算的可缩放性，自动并行尝试多个预处理技术和模型训练算法，从而为你的数据寻找性能最佳的监督式机器学习模型。

在本练习中，你将使用可视化界面在 Azure 机器学习工作室中进行自动化机器学习

> **注意**：还可以通过 Azure 机器学习 SDK 使用自动化机器学习。

## <a name="before-you-start"></a>开始之前

如果尚未完成[创建 Azure 机器学习工作区](01-create-a-workspace.md)练习以创建 Azure 机器学习工作区和计算实例，请完成它，并克隆本练习所需的笔记本。

## <a name="configure-compute-resources"></a>配置计算资源

若要使用自动化机器学习，需要在其上运行模型训练实验的计算。

1. 使用与 Azure 订阅关联的 Microsoft 凭据登录到 [Azure 机器学习工作室](https://ml.azure.com?azure-portal=true)，然后选择你的 Azure 机器学习工作区。
2. 在 Azure 机器学习工作室中，查看“计算”页面，然后在“计算实例”选项卡上，启动计算实例（如果尚未运行） 。 你将使用此计算实例来测试经过训练的模型。
3. 在计算实例启动时，切换到“计算群集”选项卡，然后通过以下设置添加新计算群集。 你将在此群集上运行自动化机器学习试验，以利用跨多个计算节点分布训练运行的功能：
    - 位置：与工作区的位置相同
    - **虚拟机层**：专用
    - 虚拟机类型：CPU
    - 虚拟机大小：Standard_DS11_v2
    - 计算名称：输入唯一名称
    - 节点数下限：0
    - **节点数上限**：2
    - **缩减前的空闲秒数**：120
    - **启用 SSH 访问**：未选定

## <a name="create-a-dataset"></a>创建数据集

拥有可用于处理数据的计算资源后，需要通过一种方法来存储和引入要处理的数据。

1. 在 Web 浏览器的 https://aka.ms/diabetes-data 中查看以逗号分隔的数据。 然后将此数据保存为名为 diabetes.csv 的本地文件（保存位置并不重要）。
2. 在 Azure 机器学习工作室中，查看“数据集”页面。 数据集表示计划在 Azure ML 中使用的特定数据文件或表。
3. 可使用以下设置从本地文件创建新数据集：
    * **基本信息**：
        * **名称**：糖尿病数据集
        * 数据集类型：表格
        * 说明：糖尿病数据
    * 数据存储和文件选择：
        * 选择或创建数据存储：当前选择的数据存储
        * **为数据集选择文件**：浏览到下载的 diabetes.csv 文件。
        * 上传路径：保留默认选择
        * 跳过数据验证：未选择
    * 设置和预览：
        * **文件格式**：分隔
        * **分隔符**：逗号
        * **编码**：UTF-8
        * 列标题：只有第一个文件有标题
        * **跳过行**：无
    * **架构**：
        * 包含除“路径”以外的所有列
        * 查看自动检测的类型
    * **确认详细信息**：
        * 创建后不分析数据集
4. 创建数据集之后，打开它并查看“浏览”页面，以查看数据示例。 此数据表示已接受糖尿病测试的患者的详细信息，你将使用它来训练一个模型，该模型可根据临床测量值预测患者糖尿病测试呈阳性的可能性。

    > **注意**：你可以生成数据集的配置文件以查看更多统计详细信息。

## <a name="run-an-automated-machine-learning-experiment"></a>运行自动化机器学习试验

在 Azure 机器学习中，运行的操作称为“试验”。 按照以下步骤运行一个试验，该试验使用自动化机器学习来训练用于预测糖尿病诊断结果的分类模型。

1. 在 Azure 机器学习工作室中，查看“自动化 ML”页（位于“作者”下） 。
2. 通过以下设置新建自动化 ML 运行：
    - **选择数据集**：
        - **数据集**：糖尿病数据集
    - **配置运行**：
        - **新的试验名称**：mslearn-automl-diabetes
        - **目标列**：糖尿病患者（这是模型将要训练的标签，用于预测）
        - **选择计算类型**：计算群集
        - **选择 Azure ML 计算群集**：你之前创建的计算群集
    - **任务类型和设置**：
        - **任务类型**：分类
        - 选择“查看附加配置设置”，打开“附加配置” ：
            - **主要指标**：选择“AUC_Weighted”（稍后详细介绍该指标！）
            - **解释最佳模型**：已选择 - 此选项会导致自动化机器学习计算最佳模型的特征重要性；使确定每个要素对预测标签的影响成为可能。
            - **使用所有受支持的模型**：未选择 - 我们将限制试验，仅尝试一些特定的算法<u></u>。
            - **允许的模型**：仅选择 LogisticRegression 和 RandomForest 。 这些将是试验中尝试的唯一算法。
            - **退出条件**：
                - 训练作业时间（小时）：0.5 - 这会导致实验最多 30 分钟后结束。
                - **指标分数阈值**：0.90 - 如果模型的加权 AUC 指标达到 90% 及以上，则会导致试验结束。
        - 选择“查看特征化设置”，打开“特征化” ：
            - **启用特征化**：已选择 - 这会导致 Azure 机器学习在训练前自动预处理功能。
    - **选择验证和测试类型**：
        - **验证类型**：定型-验证拆分
        - **验证数据百分比**：30
        - **测试数据集**：不需要测试数据集

3. 自动化 ML 运行详细信息提交完成后，它将自动启动。 可以在“属性”窗格中观察运行状态。
4. 当运行状态更改为“正在运行”时，查看“模型”选项卡，并在尝试训练算法和预处理步骤的每个可能组合和评估生成的模型的性能时进行观察。 页面会定期自动刷新，但你也可以选择“&#8635; 刷新”。 由于需要初始化群集节点并且数据特征化过程完成后才能开始训练，因此模型可能需要十分钟左右的时间才能开始显示。 这段时间正好可以喝杯咖啡休息一下！
5. 等待试验完成。

## <a name="review-the-best-model"></a>查看最佳模型

试验完成后，你可以查看生成的性能最佳的模型（请注意，在本例中，我们使用退出条件来停止试验 - 因此，试验找到的“最佳”模型可能不是最佳模型，而只是在本练习允许的时间和指标限制内找到的最佳模型！）。

1. 在自动化机器学习运行的“详细信息”选项卡上，记下最佳模型摘要。
2. 选择最佳模型的算法名称，以查看生成该模型的子运行。

    基于指定的评估指标 (AUC_Weighted) 确定最佳模型。 为了计算此指标，训练过程使用一些数据来训练模型，并应用一种称为“交叉验证”的技术，以迭代方式测试训练模型，其中包括没有训练的数据，并将预测值与实际已知值进行比较。 从这些比较中，可以列出真阳性、假阳性、真阴性和假阴性的混淆矩阵，并计算其他分类指标 - 包括可比较真假率和假真率的接受者操作特征曲线 (ROC) 图。 该曲线下的区域 (AUC) 是用于评估分类性能的常用指标。
3. 在 AUC_Weighted 值旁边，选择“查看所有其他指标”以查看分类模型的其他可能评估指标的值。
4. 选择“指标”选项卡，并查看可查看的模型性能指标。 其中包括显示已验证模型的混淆矩阵的 confusion_matrix 可视化效果和包含 ROC 图表的 precision_table 可视化效果 。
5. 依次选择“解释”选项卡和“解释 ID”，然后查看“聚合重要性”页面  。 这显示了数据集中每个特征影响标签预测的程度。

## <a name="deploy-a-predictive-service"></a>部署预测服务

使用自动化机器学习来训练某些模型后，可以部署性能最佳的模型作为服务，供客户端应用程序使用。

> **注意**：在 Azure 机器学习中，你可以将服务部署为 Azure 容器实例 (ACI) 或 Azure Kubernetes Service (AKS) 群集。 对于生产场景，建议使用 AKS 部署，为此必须创建“推理群集”计算目标。 本练习将使用 ACI 服务，它是适用于测试的部署目标，不需要你创建推理群集。

1. 选择生成了最佳模型的运行的“详细信息”选项卡。
2. 在“部署”选项中，使用“部署到 Web 服务”按钮部署具有以下设置的模型 ：
    - **名称**：auto-predict-diabetes
    - **说明**：预测糖尿病
    - **计算类型**：Azure 容器实例
    - **启用身份验证**：选定
    - **使用自定义部署资产**：未选定
3. 等待部署开始 - 这可能需要几秒钟。 然后，在“模型”选项卡上的“模型摘要”部分中，观察“auto-predict-diabetes”服务的“部署状态”，它应为“正在运行”    。 等待此状态更改为“成功”。 你需要定期选择“&#8635; 刷新”。  备注：可能需要一点时间 - 请耐心等待！
4. 在 Azure 机器学习工作室中，查看“终结点”页并选择“auto-predict-diabetes”实时终结点 。 然后选择“使用”选项卡，并在此记下以下信息。 需要此信息才能从客户端应用程序连接到已部署的服务。
    - 服务的 REST 终结点
    - 服务的主密钥
5. 请注意，你可以使用这些值旁的 &#10697; 链接将它们复制到剪贴板。

## <a name="test-the-deployed-service"></a>测试已部署的服务

部署服务后，可以使用一些简单的代码对其进行测试。

1. 在浏览器中打开“auto-predict-diabetes”服务页的“使用”页后，打开一个新的浏览器选项卡并打开 Azure 机器学习工作室的第二个实例 。 然后，在新的选项卡中，查看“笔记本”页面。
2. 在“笔记本”页面的“我的文件”下，浏览到克隆的笔记本存储库所在的 /users/your-user-name/mslearn-dp100 文件夹，然后打开“获取 AutoML 预测”笔记本   。
3. 打开笔记本后，请确保在“计算”框中选中之前创建的计算实例，并且其状态为“正在运行” 。
4. 在笔记本中，将 ENDPOINT 和 PRIMARY_KEY 占位符替换为服务的值（可从“终结点”页面上的“使用”选项卡复制这些值）  。
5. 运行代码单元并查看 Web 服务返回的输出。

## <a name="clean-up"></a>清理

你创建的 Web 服务托管于“Azure 容器实例”中。 如果不打算进一步试验它，应删除终结点以避免产生不必要的 Azure 使用量。 此外，在再次需要计算实例之前，还应停止该实例。

1. 在 Azure 机器学习工作室中的“终结点”选项卡上，选择“auto-predict-diabetes”终结点 。 然后选择“删除”(&#128465;)，并确认是否要删除该终结点。
2. 在“计算”页上的“计算实例”选项卡上，选择计算实例，然后选择“停止”。

> **注意**：停止计算可确保不会向你的订阅收取计算资源的费用。 但是，只要订阅中存在 Azure 机器学习工作区，就会向你收取少量数据存储费用。 如果已完成对 Azure 机器学习的探索，可以删除 Azure 机器学习工作区和关联的资源。 但是，如果计划完成本系列中的任何其他实验室，则需要先重复[创建 Azure 机器学习工作区](01-create-a-workspace.md)练习来创建工作区并准备环境。