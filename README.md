# AIreview
AIにレビューを読ませて有用・非有用を判定  

手順1 必要なライブラリをインポートする。  

手順2 Google driveと接続する。  

手順3 API接続を行う。  

手順4 dfにレビューデータを入れる  

　　　df = pd.read_csv("ここ")  
   
手順5　有用・非有用を判定する関数を設定する。
　　　なお、関数内の 
   
　　　 review_text = f"タイトル: {本文: {review_data['review']}"において、  
    
　　　['review']の中身を御社のデータのURLを貼り、カラム名を変更する。  
   　　
     また、プロンプトの内容も工夫できる。
     prompt = f"""
     {review_text}
     """
      {review_text}の部分の指示を変えることで、精度をより上げていける。
     
     
