// html code

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>GitHub榜单</title>
    <script src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
    <link rel="stylesheet" href="https://at.alicdn.com/t/font_1310238_q58n1opcu2.css">
    <link rel="stylesheet" href="GitHubList.css">
</head>
<body>
    <main>
        <section id="repo-board">
            <div class="container">
                <!-- <div class="item">
                    <a href="#">
                        <div class="order"><span>1</span></div>
                        <div class="detail">
                            <h2>bootstrap</h2>
                            <div class="description">The most popular HTML, CSS, and JavaScript framework for developing responsive, mobile first projects on the web.</div>
                            <div class="star-collect">134797 star</div>
                        </div>
                    </a>
                </div> -->
            </div>
            <div class="loading">
                <span class="iconfont icon-loading"></span>
            </div>
        </section>
        <section id="user-board">
            <div class="container">

            </div>  
            <div class="loading">
                <span class="iconfont icon-loading"></span>
            </div>
        </section>
        <section id="search">
            <div class="wrap">
                <div class="search-area">
                    <input type="text" placeholder="Search repos">
                    <span class="button">Search</span>
                </div>
                <div class="search-result">

                </div>
            </div>
            <div class="loading">
                <span class="iconfont icon-loading"></span>
            </div>
        </section>
    </main>

    <footer>
        <div class="active">
            <span class="iconfont icon-top"></span><span>Top Repos</span>
        </div>
        <div>
            <span class="iconfont icon-user"></span><span>Top Users</span>
        </div>
        <div>
            <span class="iconfont icon-search"></span><span>Search</span>
        </div>
    </footer>

    <script src="GitHubList.js"></script>
</body>
</html>

// css code

* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

a {
    text-decoration: none;
    color: #333;
}

html, body, main {
    position: relative;
    height: 100%;
    background-color: #fff;
}

body {
    font-size: 12px;
    line-height: 1.2;
}

main>section {
    height: calc(100% - 45px);
    padding: 10px;
    background-color: #fff;
    overflow: scroll;
    -webkit-overflow-scrolling: touch;
}

main>section {
    display: none;
}

#repo-board {
    display: block
}

.item {
    border-bottom: 1px solid #ccc;
    padding-bottom: 10px;
    padding-top: 10px;
    display: flex;
}

.item>a {
    display: flex;
}

.item .order {
    display: flex;
    align-items: center;
}

.item .order span {
    padding: 0 4px;
    color: #fff;
    background: green;
}

/* .item a {
    width: 100%;
    padding: 10px;
    border-bottom: 1px solid #ccc;
    display: flex;
    align-items: center;
} 

.item a .order {
    padding: 0 5px;
    background: green;
    color: #fff;
    text-align: center;
} */

.item .detail {
    padding-left: 10px;
}

.item .detail .star-collect {
    color: #999;
}

.item .cover,
.item .cover img {
    width: 50px;
    border-radius: 3px;
}

/* .item .user-name {
    height: 50px;
} */

#search {
    padding-top: 10px;
}

.wrap .search-area {
    position: relative;
    color: #333;
    padding-bottom: 10px;
    border-bottom: 1px solid #ccc;
}

.wrap .search-area input {
    width: calc(100% - 50px);
    padding: 8px;
    background-color: #eee;
    border: none;
    border-radius: 2px;
    box-shadow: none;
    outline: none;
}

.wrap .search-area .button {
    position: absolute;
    right: 0;
    padding: 8px 10px;
    background: #FF5722;
    color: #fff;
    border-radius: 2px;
    cursor: pointer;
}

.loading {
    text-align: center;
    padding-top: 10px;
    display: none;
}

.loading span {
    display: inline-block;
    animation: 1s rotate linear infinite;
}

@keyframes rotate {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
}

footer {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 45px;
    border-top: 1px solid #ccc;
    background-color: #fff;
    display: flex;
}

footer>div {
    flex: 1;
    padding-top: 6px;
    font-size: 12px;
    color: #666;
    text-align: center;
}

footer>div.active {
    color: #FF5722;
}

footer>div>span {
    display: block;
}

// js code

var Paging = {
    init: function() {
        this.$element = $('footer>div')
        this.$pages = $('main>section')

        this.bind()
    },
    bind: function() {
        var _this = this

        this.$element.on('click', function(e) {
            var index = $(this).index()
            $(this).addClass('active').siblings().removeClass('active')
            _this.$pages.eq(index).fadeIn().siblings().fadeOut()
        })
    }
}

var BottomTrigger = {
    isToBottom: function($view, $content) {
        return $view.height() + $view.scrollTop() + 30 > $content.height()
    }
}

var RepoBoard = {
    init: function() {
        var _this = this
        this.$container = $('#repo-board')
        this.$content = $('#repo-board .container')
        this.page = 1
        this.count = 30
        this.isFinished = false
        this.isLoading = false

        this.bind()
        this.getData(function(result) {
            _this.renderData(result)
            _this.page++
        })
    },
    bind: function() {
        var _this = this

        this.$container.on('scroll', function() {
            if (BottomTrigger.isToBottom(_this.$container, _this.$content) && !_this.isFinished && !_this.isLoading) {
                _this.getData(function(result) {
                    _this.renderData(result)
                    _this.page++

                    if (_this.page*_this.count > result.data.total_count) {
                        _this.isFinished = true
                    }
                })
            }
        })
    },
    getData: function(callback) {
        var _this = this
        this.isLoading = true
        $('#repo-board .loading').show(400)

        $.ajax({
            url: 'https://api.github.com/search/repositories?q=language:javascript&sort=stars&order=desc',
            data: {
                page: _this.page
            },
            dataType: 'jsonp'
        }).done(function(ret) {
            console.log(ret)
            _this.isLoading = false
            _this.$container.find('.loading').hide(400)
            callback(ret)
        })
    },
    renderData: function(ret) {
        var _this = this
        
        ret.data.items.forEach(function(item, index) {
            var $node = _this.createNode(item, index)
            _this.$content.append($node)
        })
    },
    createNode: function(item, index) {
        var tpl = `<div class="item">
                        <a href="#">
                            <div class="order"><span>1</span></div>
                            <div class="detail">
                                <h2>bootstrap</h2>
                                <div class="description">The most popular HTML, CSS, and JavaScript framework for developing responsive, mobile first projects on the web.</div>
                                <div class="star-collect">134797 star</div>
                            </div>
                        </a>
                    </div>`
        var $node = $(tpl)
        $node.find('.order span').text((this.page - 1)*30+index+1)
        $node.find('.detail h2').text(item.name)
        $node.find('a').attr('href', item.html_url)
        $node.find('.detail .description').text(item.description)
        $node.find('.detail .star-collect').text(item.stargazers_count)
        return $node
    }
}

var UserBoard = {
    init: function() {
        var _this = this

        this.$container = $('#user-board')
        this.$content = $('#user-board .container')
        this.page = 1
        this.count = 30
        this.isLoading = false
        this.isFinished = false

        this.bind()
        this.getData(function(result) {
            _this.renderData(result) 
            _this.page++
        })
    },
    bind: function() {
        var _this = this

        this.$container.on('scroll', function() {
            if (BottomTrigger.isToBottom(_this.$container, _this.$content) && !_this.isFinished && !_this.isLoading) {
                _this.getData(function(result) {
                    _this.renderData(result)
                    _this.page++

                    if (_this.page*_this.count > result.data.total_count) {
                        _this.isFinished = true
                    }
                })
            }
        })
    },
    getData: function(callback) {
        var _this = this
        _this.isLoading = true
        _this.$container.find('.loading').show(400)
        $.ajax({
            url: 'https://api.github.com/search/users?q=followers:>1000+location:china+language:javascript',
            data: {
                page: _this.page
            },
            dataType: 'jsonp'
        }).done(function(ret) {
            console.log(ret)
            _this.isLoading = false
            _this.$container.find('.loading').hide(400)
            callback(ret)
            _this.page++
        })
    },
    renderData: function(ret) {
        var _this = this

        ret.data.items.forEach(function(item) {
            var $node = _this.createNode(item)
            _this.$content.append($node)
        })
    },
    createNode: function(item) {
        var tpl = `<div class="item">
                        <a href="#">
                            <div class="cover"><img src="" alt=""></div>
                            <div class="detail">
                                <h2 class="user-name">bootstrap</h2>
                            </div>
                        </a>
                    </div>`
        var $node = $(tpl)
        $node.find('.cover img').attr('src', item.avatar_url)
        $node.find('.detail h2').text(item.login)
        $node.find('a').attr('href', item.repos_url)

        return $node
    }
}

var Search = {
    init: function() {
        this.$container = $('#search')
        this.$content = this.$container.find('.search-result')
        this.keyword = ''
        this.page = 1
        this.isLoading = false

        this.bind()
    },
    bind: function() {
        var _this = this

        this.$container.find('.button').on('click', function() {
            $('#search').find('.loading').fadeIn(400)
            if (!_this.isLoading) {
                _this.getData(function(result) {
                    _this.renderData(result)
                })
            }
        })

        this.$container.find('input').on('keyup', function(e) {
            if (e.key === 'Enter') {
                _this.getData(function(result) {
                    _this.renderData(result)
                })
            }
        })
    },
    getData: function(callback) {
        var _this = this 
        var keyword = this.$container.find('input').val()
        this.isLoading = true

        $.ajax({
            url: `https://api.github.com/search/repositories?q=${keyword}+language:javascript&sort=stars&order=desc&${_this.page}`,
            dataType: 'jsonp'
        }).done(function(ret) {
            console.log(ret)
            _this.isLoading = false
            $('#search').find('.loading').fadeOut(400)
            callback(ret)
        })
    },
    renderData: function(ret) {
        var _this = this

        ret.data.items.forEach(function(item, index) {
            var $node = _this.createNode(item, index)
            _this.$content.append($node)
        })
    },
    createNode: function(item, index) {
        var tpl = `<div class="item">
                        <a href="#">
                            <div class="order"><span>1</span></div>
                            <div class="detail">
                                <h2>bootstrap</h2>
                                <div class="description">The most popular HTML, CSS, and JavaScript framework for developing responsive, mobile first projects on the web.</div>
                                <div><span class="star-count">134797</span> star</div>
                            </div>
                        </a>
                    </div>`
        var $node = $(tpl)
        $node.find('.order span').text((this.page - 1)*30+index+1)
        $node.find('.detail h2').text(item.name)
        $node.find('item a').attr('href', item.html_url)
        $node.find('.detail .description').text(item.description)
        $node.find('.detail .star-collect').text(item.stargazers_count)
        return $node
    }
}

var App = {
    init: function() {
        Paging.init()
        RepoBoard.init()
        UserBoard.init()
        Search.init()
    }
}

App.init()

