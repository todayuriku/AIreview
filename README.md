# AIreview
AIにレビューを読ませて有用・非有用を判定  

手順1 必要なライブラリをインポートする。  

手順2 Google driveと接続する。  

手順3 API接続を行う。  

手順4 dfにレビューデータを入れる  

　　　df = pd.read_csv("ここ")  
   
手順5　有用・非有用を判定する関数を設定する。なお、プロンプト内は自由に設定できる。  


      promt = f"""
      ここ
      """
手順6  review_text = f"タイトル: {本文: {review_data['review']}"において、  

　　　['review']の中身を御社のデータのURLを貼り、変更する。
