# pandas_spreadsheet
スプレッドシートの計算をPandasで再現してみた  

import pandas as pd  

### 使用する日付、引落入力、預入のDataFrameを作成。  
### 実務ではこちらのデータはcsv等で取り込むイメージです  
df1 = pd.DataFrame({  
                "date":["4/1", "4/8", "4/15", "4/22", "4/29", "5/6", "5/13", "5/20", "5/27", "6/3"],  
                "withd_inp":[2000, 0, 4000, 0, 1500,3500,0,6000,0,4000],  
                "deposit":[0,3000,0,0,2000,0,0,0,8000,0]  
                  })  

### 引落入力と比較するための"引出制限 3,000円" をlimitカラムに入力  
### 次にdf の要素に min関数を適用するためにカラムに入力している  
df1["limit"] = 3000  

### 要素同士を比較して小さい方を実際の引落に入力  
df1["withd_act"] = df1[["withd_inp", "limit"]].min(axis=1)  

### for文を適用するために 今週頭の残高と、今週終の残高のカラムを作成する  
### None を入力する必然性はないが、数字と区別するために None を入力しておく  
df1["accnt_head"] = None  
df1["accnt_tail"] = None  

### 今週頭の残高と、今週終の残高の作成  
### エラーが出るが計算は行われる  
for i in range(len(df1)):  
    if i == 0:  
        df1["accnt_head"][0] = 3000  
    else:  
        df1["accnt_head"][i] = df1["accnt_tail"][i-1]  
    df1["accnt_tail"][i] = df1["accnt_head"][i] - df1["withd_act"][i] + df1["deposit"][i]  

### pandasの要素に、自作の関数を適用したい場合  
def 自作の関数:  
     設定したい処理  

df['X'] = df['A'].map(自作の関数)  

### pandasの要素に、無名関数 lambda を適用したい場合  
df["X"] = df["A"].map(lambda x : 設定したい処理 )  

### 単純にmax()、min()関数を設定するだけなら関数を作成する必要はありませんが、要素を計算した後の数値に対してmax()、min()を設定したい場合には、以下のような方法も使えます。  
def 関数名(row): #rowはDataFrame型にあたります  
    return max((row['A'] - row['B'])*0.9, 0)   

df['X'] = df.apply(関数名, axis=1)  
