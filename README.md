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
      type: 'GET',
      datatype: 'json',
      data: {
        //検索キーワード↓
        keyword: SearchTitle,
        hits: 20,
        page: page,
        timeout: 10000,
        // 「区分:サービス固有パラメーター」↓
        applicationId: '1019399324990976605',
        booksGenreId: '001'
      },
    }).done(function(data) {
      if(data.count > 0){
        Display(data);                //表示を行う関数
      }else{                          //レスポンス データエラー
        var addFailMes = "<div class='message'><p>検索結果が見つかりませんでした。<br>別のキーワードで検索して下さい。</p></div>";
        $('.wrap').find('.search').after(addFailMes);
      }
    }).fail(function(jqXHR, textStatus, errorThrown){
      if(jqXHR.readyState != 4){
        var addErrorMas = "<div class='message'><p>!!通信エラー<br>データが取得できませんでした...</p></div>";
      }else{
        var errorMes = ErrorKind(jqXHR.responseJSON.error_description);//Return ErrorMessage...
        var addErrorMas = "<div class='message'><p>" + errorMes + "</p></div>";
      }
      $('.wrap').find('.search').after(addErrorMas);
    });
  }

  function Initialize(page){//htmlの初期化
    var anyMes = $('.wrap').find('.message');//エラーメッセ
    if
    (anyMes != null){
      $('.wrap').find('.message').remove();
    }
    var anyList = $('.wrap').find('.lists');//本
    if(anyList != null && page <= 1){
      $('.wrap').find('.lists').empty();
    }
  }

  function checkWord(word){
    if(saveName === word){
      page++;
    }
    else{
      page = 1;
      saveName = word;
    }
    return page;
  }

  function Display(dataJson){//メインのList画像表示
    var addHtml = "";
    $.each(dataJson.Items,function(i, dataItems){

      addHtml += "<li class='lists__item'><div class='lists__item__inner'><a href='" + dataItems.Item.itemUrl + "' class='lists__item__link' target='_blank'><img src='" + dataItems.Item.largeImageUrl + "' class='lists__item__img' alt='" + dataItems.Item.title + "'><p class='lists__item__detail'>作品名：　" + dataItems.Item.title + "</p><p class='lists__item__detail'>作者　：　" + dataItems.Item.author + "</p><p class='lists__item__detail'>出版社：　" + dataItems.Item.publisherName + "</p><a></div></li>";
    });

    var saveList = $('.wrap').find('.lists').html();
    addHtml += saveList;

    $('.lists').html(addHtml);
  }

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
</body>
</html>


```
