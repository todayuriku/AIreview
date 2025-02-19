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

      ⑻# Gemini Proモデルの初期化
       　# gemini_pro = genai.GenerativeModel("gemini-1.5-flash")
         gemini_pro = genai.GenerativeModel("gemini-1.0-pro")  

         # APIのテスト
         　prompt = "こんにちは"
           response = gemini_pro.generate_content(prompt)
           print(response.text)
　　　　　　
　　　　　　

      ⑼ from google.colab import drive
         　drive.mount('/content/drive')



手順4 dfにレビューデータを入れる 

      ⑽　df = pd.read_csv("ここ")
   
手順5　有用・非有用を判定する関数を設定する。

　　　⑾　@retry.Retry(predicate=retry.if_exception_type(exceptions.ResourceExhausted))
　　　　　def analyze_review(review_data):

    
    review_text = f"タイトル: {本文: {review_data['review']}"
    prompt = f"""
    以下に入力する商品レビューの本文が、製品開発に有用であれば、True、非有用であれば、Falseと出力してください。ただし、以下の例文のようなただ褒めるだけのようなレビューは非有用と判断してください。(例)すごい。
    {review_text}
    """
    try:
        response = gemini_pro.generate_content(prompt)
        return response.text.strip()
    except Exception as e:
        print(f"Error analyzing review: {e}")
        return "ERROR"

def analyze_reviews_batch(df, start_idx=0, batch_size=10):
    """指定された開始位置からバッチサイズ分のレビューを分析する

    Args:
        df: 分析対象のデータフレーム
        start_idx: 開始インデックス
        batch_size: バッチサイズ
    Returns:
        analyzed_df: 分析結果を含むデータフレーム
        last_idx: 最後に処理したインデックス
    """
    end_idx = min(start_idx + batch_size, len(df))
    batch = df.iloc[start_idx:end_idx].copy()

    results = []
    for _, row in tqdm(batch.iterrows(), total=len(batch), desc=f"Analyzing {start_idx} to {end_idx}"):
        result = analyze_review(row)
        results.append(result)
        time.sleep(3)  # API制限対策

    batch['analysis_result'] = results
    return batch, end_idx

# 進捗を保存しながら分析を実行
def run_analysis_with_checkpoints(input_df, checkpoint_path='review_analysis_checkpoint.csv',
                                batch_size=10, start_from=0):
    """チェックポイントを保存しながらレビュー分析を実行する

    Args:
        input_df: 入力データフレーム
        checkpoint_path: チェックポイントファイルのパス
        batch_size: バッチサイズ
        start_from: 開始インデックス
    """
    all_results = []
    current_idx = start_from

    try:
        while current_idx < len(input_df):
            # バッチ処理の実行
            analyzed_batch, last_idx = analyze_reviews_batch(
                input_df,
                start_idx=current_idx,
                batch_size=batch_size
            )

            # 結果の保存
            all_results.append(analyzed_batch)
            current_results = pd.concat(all_results)
            current_results.to_csv(checkpoint_path)

            print(f"Checkpoint saved at index {last_idx}")
            current_idx = last_idx

            time.sleep(10)  # バッチ間の待機

    except Exception as e:
        print(f"Error occurred at index {current_idx}: {e}")
        print("You can resume from this index later")

    finally:
        # 最終結果の結合
        final_results = pd.concat(all_results) if all_results else pd.DataFrame()
        return final_results

　　　なお、関数内の review_text = f"タイトル: {本文: {review_data['review']}"において、  
    
　　　['review']の中身を御社のデータのURLを貼り、カラム名を変更する。  
   　　
     また、プロンプトの内容も工夫できる。
     prompt = f"""
     {review_text}
     """
      {review_text}の部分の指示を変えることで、精度をより上げていける。
     
     
