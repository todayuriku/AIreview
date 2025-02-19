# AIreview
AIにレビューを読ませて有用・非有用を判定  


手順内の①～　が実行可能なコードになります。  
コードの下にはそれぞれのコードの説明を記載しています。  


手順1 必要なライブラリをインポートする。  

      ⑴# 必要なパッケージのインポート
      import pandas as pd
      import google.generativeai as genai
      from google.colab import drive, userdata
      import time
      import random
      from tqdm import tqdm
      from google.api_core import exceptions, retry   

  
  　　　以上の各パッケージの役割について簡単に説明します。  
    
   　・pandas: データ操作と解析のためのライブラリ。データフレームを使用してデータを効率的に管理できます。  
　　 ・google.generativeai: Googleの生成AIにアクセスするためのライブラリ。  
　　 ・google.colab: Google Colab環境での特定の機能を提供するライブラリ。  
　　 ・ time: 時間に関連する機能を提供する標準ライブラリ。プログラムの実行時間を測定する際などに使用します。  
　　 ・ random: ランダムな数や選択を生成するための標準ライブラリ。データのシャッフルやサンプリングに利用されます。  
　　 ・tqdm: ループの進行状況を表示するためのライブラリ。長い処理の進捗を視覚的に示すことができます。  
   　・google.api_core.exceptions: Google APIのエラーハンドリングを行うための例外クラスを提供します。  
     ・google.api_core.retry: API呼び出しの再試行を管理するための機能を提供します。  

     ⑵ import numpy as np　import pandas as pd 
     
以上のコードは、numpyとpandasライブラリをインポートするためのものです。  
以上のライブラリについて簡単に説明します。  

・numpy: 数値計算や配列操作のためのライブラリです。

・pandas: データ操作と解析のためのライブラリです。特に、表形式のデータ（データフレーム）を扱うのに便利です。  

    ⑶import os from datetime import datetime  

    
以上のモジュールについて簡単に説明します。  

・os: オペレーティングシステムとの対話を行うための標準ライブラリです。  
　　　ファイルやディレクトリの操作、環境変数の取得、プロセス管理などに利用されます。

・datetime: 日付と時間を扱うための標準ライブラリです。日時の計算やフォーマット、現在の日時の取得などが可能です。



     
    

      

手順2 Google driveと接続する。  

      ⑷# Google Driveのマウントとパッケージのインストール
        drive.mount('/content/drive')
        !pip install google-generativeai  

      ⑸import google.colab
        import google.generativeai as genai  
        

⑸のコードは、Google Colabの特定の機能を使用するためのgoogle.colabモジュールと、Googleの生成AIにアクセスするためのgoogle.generativeaiモジュールをインポートするものです。これらのモジュールについて簡単に説明します。

・google.colab: Google Colab環境で特定の機能を提供するためのモジュールです。主に、ファイルのアップロード、Google Driveとの連携、ユーザーデータの管理などに使用されます。

・google.generativeai: Googleの生成AIモデルにアクセスするためのライブラリです。自然言語処理や生成タスクを実行するための機能を提供します。  


     ⑹# 行の表示数を設定（Noneを指定すると全ての行を表示）
       pd.set_option('display.max_rows', None)
     # 列の表示数を設定（Noneを指定すると全ての列を表示）
     pd.set_option('display.max_columns', None)


手順3 API接続を行う。  

      ⑺# API鍵の設定
     　　GOOGLE_API_KEY = userdata.get('GOOGLE_API_KEY')
     　　genai.configure(api_key=GOOGLE_API_KEY)

       

# APIのテスト
prompt = "こんにちは"
response = gemini_pro.generate_content(prompt)
print(response.text)

手順4 dfにレビューデータを入れる  

　　　df = pd.read_csv("ここ")  
   
手順5　有用・非有用を判定する関数を設定する。  

　　　なお、関数内の review_text = f"タイトル: {本文: {review_data['review']}"において、  
    
　　　['review']の中身を御社のデータのURLを貼り、カラム名を変更する。  
   　　
     また、プロンプトの内容も工夫できる。
     prompt = f"""
     {review_text}
     """
      {review_text}の部分の指示を変えることで、精度をより上げていける。
     
     
