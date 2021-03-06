---
title: "Team Data Science Process ライフサイクルのモデリング ステージ - Azure | Microsoft Docs"
description: "データ サイエンス プロジェクトのモデリング ステージの目標、タスク、成果物。"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2017
ms.author: bradsev;
ms.openlocfilehash: 0c855faa96d7feb9f762ecaed7654669a5a8a5b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="modeling"></a>モデリング

このトピックでは、Team Data Science Process の**モデリング ステージ**に関連付けられている目標、タスク、成果物のアウトラインを示します。 このプロセスには、データ サイエンス プロジェクトを体系化するために使用できる推奨ライフサイクルが用意されています。 ライフサイクルは、プロジェクトで通常 (多くの場合に繰り返し) 実行される主要なステージのアウトラインを示します。

* **ビジネスの把握**
* **データの取得と理解**
* **モデリング**
* **デプロイ**
* **顧客による受け入れ**

次の図は、**Team Data Science Process ライフサイクル**を視覚的に表したものです。 

![TDSP-Lifecycle2](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>目標
* 機械学習モデルに最適なデータの特徴。
* 最も正確にターゲットを予測でき、有益な情報を提供する ML モデル。
* 運用環境に適した ML モデル。

## <a name="how-to-do-it"></a>方法
このステージでは、以下に示す 3 つの主な課題に取り組みます。

* **特徴エンジニアリング**: モデルをトレーニングしやすくするために生データからデータの特徴を作成します。
* **モデル トレーニング**: 成功のメトリックを比較することで、最も正確に質問に回答できるモデルを見つけます。
* モデルが**運用環境に適している**かどうかを判断する。

### <a name="31-feature-engineering"></a>3.1 特徴エンジニアリング
特徴エンジニアリングでは、分析に使用する特徴を作成するために、未加工の変数の追加、集計、変換を行います。 何がモデルの推進要因となっているかを把握したい場合、それぞれの特徴の相互関係と、それらの特徴が機械学習アルゴリズムでどのように使用されるかを理解する必要があります。 このステップでは、特定分野の専門知識とデータの調査ステップから得られた知見を創造的に組み合わせる必要があります。 これは、関連性の低い変数が増えすぎないように注意しながら、情報量の多い変数を見つけて追加する作業であり、バランス感覚が求められます。 情報量の多い変数は結果の改善に貢献しますが、関連性の低い変数は不要なノイズがモデルに混入する原因となります。 また、これらの特徴は、スコア付け中に取得した新しいデータがあれば、そのデータに対して生成する必要もあります。 そのため、これらの特徴の生成は、スコア付け時に使用できるデータのみに基づいて行われます。 各種 Azure データ テクノロジを利用して特徴エンジニアリングを行う場合の技術的なガイダンスについては、[データ サイエンス プロセスにおける特徴エンジニアリング](create-features.md)に関するページを参照してください。 

### <a name="32-model-training"></a>3.2 モデル トレーニング
回答しようとしている質問の種類に応じて、数多くのモデリング アルゴリズムが利用できます。 アルゴリズムの選択に関するガイダンスについては、「[Microsoft Azure Machine Learning のアルゴリズムの選択方法](../studio/algorithm-choice.md)」を参照してください。 これは Azure Machine Learning に関する記事ですが、記載されているガイダンスはすべての Machine Learning プロジェクトにも活用できます。 

モデル トレーニングのプロセスには、以下のステップが含まれます。 

* モデリング用のトレーニング データセットとテスト データセットにランダムに**入力データを分割する**。
* トレーニング データセットを使用して**モデルを構築する**。
* 競合する一連の機械学習アルゴリズムを、現状のデータで目的の質問に回答できるよう調整された、各種の関連するチューニング パラメーター (パラメーター スイープと呼ばれる) と共に (トレーニング データセットとテスト データセットで) **評価する**。
* 別々の方法での成功のメトリックを比較し、質問に回答するための **"最適" なソリューションを判定する**。

> [!NOTE]
> **漏えいの防止**: トレーニング データセットの外部のデータを含めることでデータ漏えいが発生する可能性があり、それによってモデルまたは機械学習アルゴリズムが非現実的なほど良好な予測を行うことがあります。 信じられないほど良好な予測結果が得られた場合に、データ サイエンティストが不安を感じる理由として代表的なのが、この漏えいです。 これらの依存関係は検出しにくい場合があります。 漏えいを回避するには、多くの場合、分析データセットの構築、モデルの作成、精度の評価を繰り返し行うことが必要になります。 
> 
> 

Microsoft では、複数のアルゴリズムとパラメーター スイープを介して実行し、ベースライン モデルを生成することができる、[Automated Modeling and Reporting ツール](https://github.com/Azure/Azure-TDSP-Utilities/blob/master/DataScienceUtilities/Modeling)を TDSP と共に提供しています。 このツールでは、変数の重要性を含む、各モデルおよびパラメーターの組み合わせによるパフォーマンスを要約した、ベースライン モデリング レポートも生成されます。 特徴エンジニアリングをさらに促進することができるため、このプロセスも繰り返し実行されます。 

## <a name="artifacts"></a>アーティファクト
このステージで生成されるアーティファクトには、以下のものが含まれます。

* [**特徴セット**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/DataReport/Data%20Defintion.md#feature-sets): モデリング用に作成された特徴は、**データ定義**レポートの「**Feature Set** 」(特徴セット) セクションに記載されます。 これには、特徴を生成するコードへのポインターと、特徴の生成方法についての説明が含まれます。
* [**モデル レポート**](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Model/Model%201/Model%20Report.md): 試行した各モデルについて、各実験の詳細を提供する標準のテンプレート ベースのレポートが生成されます。
* **チェックポイント判定**: モデルのパフォーマンスが、実稼働システムにデプロイするのに十分かどうかを評価します。 主な考慮事項の一部を以下に示します。
  * テスト データから判断して、質問に対するモデルの回答に十分な確実性があるか。 
  * 任意の代替手法 (追加データを収集する、特徴エンジニアリングをさらに実行する、他のアルゴリズムで実験する) を試す必要があるか。

## <a name="next-steps"></a>次のステップ

Team Data Science Process のライフサイクルの各ステップへのリンクを次に示します。

* [1.ビジネスの把握](lifecycle-business-understanding.md)
* [2.データの取得と理解](lifecycle-data.md)
* [3.モデリング](lifecycle-modeling.md)
* [4.デプロイ](lifecycle-deployment.md)
* [5.顧客による受け入れ](lifecycle-acceptance.md)

また、 **特定のシナリオ** のプロセスに伴うすべての段階をリハーサル的に最初から最後まで実証することも可能です。 これらは、[サンプル チュートリアル](walkthroughs.md)のトピックで簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 

Azure Machine Learning Studio を使用する Team Data Science Process のステップを実行する例については、[Azure ML の使用](http://aka.ms/datascienceprocess)ラーニング パスをご覧ください。 