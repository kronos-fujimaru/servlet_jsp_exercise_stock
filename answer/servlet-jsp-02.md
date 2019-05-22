# 在庫管理アプリケーション（Servlet/JSP演習）

## 解答例

#### DataSourceManager.java（パッケージ：jp.shop）　※コネクションプール利用

```java
package jp.shop;

import java.sql.Connection;
import java.sql.SQLException;

import javax.naming.Context;
import javax.naming.InitialContext;
import javax.naming.NamingException;
import javax.servlet.ServletException;
import javax.sql.DataSource;

public class DataSourceManager {

    public static Connection getConnection() throws ServletException, NamingException, SQLException {
        try {
            Context context = new InitialContext();
            DataSource dataSource = (DataSource) context.lookup("java:comp/env/jdbc/mysql");
            return dataSource.getConnection();
        } catch (NamingException e) {
            throw e;
        } catch (SQLException e) {
            throw e;
        }
    }
}
```

<br>

#### list.jsp　※EL式、JSTL利用

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib uri="http://java.sun.com/jsp/jstl/fmt" prefix="fmt" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>在庫管理システム</title>
<link rel="stylesheet" href="css/style.css">
</head>
<body>
    <header>
        <h1>ケロノス雑貨 総本店</h1>
    </header>
    <nav>
        <div>
            <label>メニュー</label>
        </div>
        <ul>
            <li><a href="/stock_manage/list_stock">在庫一覧表示</a></li>
            <li><a href="/stock_manage/create_stock">在庫登録</a></li>
        </ul>
    </nav>
    <article>
        <div class="text-center">
            <h2>在庫一覧</h2>
        </div>
        <div>
            <table class="table-list block-center">
                <thead>
                    <tr>
                        <th class="width-id">ID</th>
                        <th class="width-name">商品名</th>
                        <th class="width-number">価格</th>
                        <th class="width-number">数量</th>
                        <th class="width-date">更新日</th>
                        <th class="width-btn"></th>
                    </tr>
                </thead>
                <tbody>
                    <c:forEach var="stock" items="${listStock}">
                        <tr class="tr-active">
                            <td class="width-id text-center"><a href="/stock_manage/update_stock?id=${stock.id}">${stock.id}</a></td>
                            <td class="width-name"><c:out value="${stock.item}"/></td>
                            <td class="width-number text-right"><fmt:formatNumber value="${stock.price}" pattern="\#,###,###" /></td>
                            <td class="width-number text-right">${stock.quantity}</td>
                            <td class="width-date text-center"><fmt:formatDate value="${stock.updateDate}" pattern="yyyy-MM-dd HH:mm" /></td>
                            <td class="width-btn text-center">
                                <form action="/stock_manage/delete_stock" method="post">
                                    <input type="hidden" name="id" value="${stock.id}">
                                    <button type="submit" class="btn-delete">DELETE</button>
                                </form>
                            </td>
                        </tr>
                    </c:forEach>
                </tbody>
            </table>
        </div>
        <div class="text-center">
            <p class="font-red"><c:if test="${message != null}">${message}</c:if></p>
        </div>
    </article>
    <footer>
        <label>Copyright (c) 20xx. Kelonos Co, Ltd All Rights Reserved.</label>
    </footer>
</body>
</html>
```

<br>

#### form.jsp　※EL式、JSTL利用

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>在庫管理システム</title>
<link rel="stylesheet" href="css/style.css">
</head>
<body>
    <header>
        <h1>ケロノス雑貨 総本店</h1>
    </header>
    <nav>
        <div>
            <label>メニュー</label>
        </div>
        <ul>
            <li><a href="/stock_manage/list_stock">在庫一覧表示</a></li>
            <li><a href="/stock_manage/create_stock">在庫登録</a></li>
        </ul>
    </nav>
    <article>
        <c:choose>
            <c:when test="${stock == null}">
                <div class="text-center">
                    <h2>在庫登録</h2>
                </div>
                <form action="/stock_manage/create_stock" method="post">
                    <table class="block-center">
                        <tr><td class="text-right">商品名：</td><td><p><input type="text" class="form-text" name="item" placeholder=" 20文字以内" required></p></td></tr>
                        <tr><td class="text-right">価格：</td><td><p><input type="number" class="form-number" name="price" min="0" max="1000000" value="0" required></p></td></tr>
                        <tr><td class="text-right">数量：</td><td><p><input type="number" class="form-number" name="quantity" min="0" max="100" value="0" required></p></td></tr>
                        <tr><td colspan="2" class="text-center"><p><button type="submit" class="btn-default">SEND</button></p></td></tr>
                    </table>
                </form>
            </c:when>
            <c:when test="${stock != null}">
                <div class="text-center">
                    <h2>在庫更新</h2>
                </div>
                <form action="/stock_manage/update_stock" method="post">
                    <table class="block-center">
                        <tr>
                            <td class="text-right">商品名：</td>
                            <td><p><input type="text" class="form-text" name="item" value="${stock.item}" required></p></td>
                        </tr>
                        <tr>
                            <td class="text-right">価格：</td>
                            <td><p><input type="number" class="form-number" name="price" min="0" value="${stock.price}" required></p></td>
                        </tr>
                        <tr>
                            <td class="text-right">数量：</td>
                            <td><p><input type="number" class="form-number" name="quantity" min="0" max="100" value="${stock.quantity}" required></p></td>
                        </tr>
                        <tr><td colspan="2" class="text-center"><p><button type="submit" class="btn-default">SEND</button></p></td></tr>
                    </table>
                    <input type="hidden" name="id" value="${stock.id}">
                </form>
            </c:when>
        </c:choose>
    </article>
    <footer>
        <label>Copyright (c) 20xx. Kelonos Co, Ltd All Rights Reserved.</label>
    </footer>
</body>
</html>
```
