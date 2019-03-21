# jquey_curriculum lesson10 説明用回答　完全版

```

  <script src="../../common/js/jquery.js"></script>
  <script>
  var page = 1;//取得ページ数
  var saveName = "";
  //「検索」クリック
  $('#js-search-button').on('click',function(){
    var SearchTitle = document.getElementById("js-search-word").value;//Input検索ワード
    Initialize(checkWord(SearchTitle));
    AjaxReqest(SearchTitle);

  });

  function AjaxReqest(SearchTitle){

    $.ajax({
      url
      : 'https://app.rakuten.co.jp/services/api/BooksTotal/Search/20130522',
      type: 'GET',　　　　　　　//get post の通信メソッドの違いに注意！！！
      datatype: 'json',        //送られてくるデータの形式を指定。
      data: {
        //検索キーワード↓
        keyword: SearchTitle,
        hits: 20,
        page: page,　　　　　　　//指定hits数毎に１page
        timeout: 10000,　　　　　//確認ができていないが、タイムアウト処理はajaxに必要。ずっと通信しようとしてる状態になるから。。
        // 「区分:サービス固有パラメーター」↓
        applicationId: '1019399324990976605',
        booksGenreId: '001'
      },
    }).done(function(data) {
      if(data.count > 0){
        Display(data);
      }else{                          //レスポンス データエラー
        var addFailMes = "<div class='message'><p>検索結果が見つかりませんでした。<br>別のキーワードで検索して下さい。</p></div>";
        $('.wrap').find('.search').after(addFailMes);
      }
    }).fail(function(jqXHR, textStatus, errorThrown){//第二、第三引数は使っていない。内容確認のためにつけていた。
      if(jqXHR.readyState != 4){
        var addErrorMas = "<div class='message'><p>!!通信エラー<br>データが取得できませんでした...</p></div>";
      }else{
        var errorMes = ErrorKind(jqXHR.responseJSON.error_description);//Return ErrorMessage...
        var addErrorMas = "<div class='message'><p>" + errorMes + "</p></div>";
      }
      $('.wrap').find('.search').after(addErrorMas);
    });
  }

//画面の初期化処理
//remove() empty()の違いに注意。　タグごと要素削除と、タグ内を空にする　という大きな違い！
  function Initialize(page){//htmlの初期化
    var anyMes = $('.wrap').find('.message');//エラーメッセ
    if(anyMes != null){
      $('.wrap').find('.message').remove();//全削除
    }
    var anyList = $('.wrap').find('.lists');//アイテム
    if(anyList != null && page == 1){//検索ワードが変わったら必然にpage == 1
      $('.wrap').find('.lists').empty();//空にする
    }
  }

//検索ワードの判定を行う
//ここでバリデーション処理をかけたり入力チェックをしてもいい。
  function checkWord(word){
    if(saveName === word){
      page++;//ページ数を次に
    }
    else{
      page = 1;//ページを初期化
      saveName = word;//前回ワードを今回の検索ワードに更新
    }
    return page;
  }

//表示を行う下準備と表示をする
//dataJsonを引数で受け取る
  function Display(dataJson){//メインのList画像表示
    var addHtml = "";//初期化
    //書き方は色々ありますので、自分で調べて↓
    $.each(dataJson.Items,function(i, dataItems){

      addHtml += "<li class='lists__item'><div class='lists__item__inner'><a href='" + dataItems.Item.itemUrl + "' class='lists__item__link' target='_blank'><img src='" + dataItems.Item.largeImageUrl + "' class='lists__item__img' alt='" + dataItems.Item.title + "'><p class='lists__item__detail'>作品名：　" + dataItems.Item.title + "</p><p class='lists__item__detail'>作者　：　" + dataItems.Item.author + "</p><p class='lists__item__detail'>出版社：　" + dataItems.Item.publisherName + "</p><a></div></li>";
    });

    //var saveList = $('.wrap').find('.lists').html();//前回のを取得して保存しておく
    //addHtml += saveList;//後ろに追加
    //$('.lists').html(addHtml);//htmlメソッドは対象に対し、上書きを行うので、前回のが消える。。
    //↓こっちを使う！
    $(' lists').append(addHtml);//こっちの方があってる、追加するメソッド　更新をかけたいのならこっちだろう。。
  }

//エラーチェックは、個人のできるレベルまででいい。
//エラーの種類を調べ、そのエラーメッセージをconsoleに表示、取得できるようにして、エラー毎に判定を加える。。
//本来はある程度エラーに対して改善処理を入れたりなど、補助の機能をそこで付与できる。
//今回は判定して日本語に直して出力をしているのみ。。これだけでも日本人ユーザーには大きなこと！！

  function ErrorKind(){//エラーの種類分け
    switch(jqXHR.responseJSON.error_description){
      case "keyword length must be over 2 characters":
        return "２文字以上入力してください。";
      case "keyword must be set":
        return "キーワードを入力してください。";
      case "each of multiple keyword parameters length must be over 2 characters":
        return "複数ワードは入力できません。";
      case "number of allowed requests has been exceeded for this API. please try again soon.":
        return "連打しないで```\(>o<)/```";
      default:
        return "予期せぬエラーが発生しました。";
    }
  }

  </script>

```
