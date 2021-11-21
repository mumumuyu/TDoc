# JDBC

### 	基础经典增删查改(jsp)

##### 		1.增

```java
Class.forName("com.mysql.cj.jdbc.Driver");
        System.out.println("Driver loaded");
        Connection connection = DriverManager.getConnection
                ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
        Statement stmt = connection.createStatement();    //Create a statement
        String sql = "insert into student(id, name, sex) values (?,?,?);";    //insert
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setString(1, "003");
        preparedStatement.setString(2, "lgd");
        preparedStatement.setString(3, "男");
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            out.println("成功添加" + i + "行");
        }
        preparedStatement.close();
        stmt.close();
        connection.close();}
    catch (SQLException e){
            e.printStackTrace();
        }
```



##### 2.删

```java
try {
        Class.forName("com.mysql.cj.jdbc.Driver");
        System.out.println("Driver loaded");
        Connection connection = DriverManager.getConnection
                ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
        Statement stmt = connection.createStatement();    //Create a statement
        String sql = "delete from student where id = '003';";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            out.println("成功删除" + i + "行");
        }
        preparedStatement.close();
        stmt.close();
        connection.close();}
    catch (SQLException e){
        e.printStackTrace();
    }
```



##### 3.查

```java
try {
    Class.forName("com.mysql.cj.jdbc.Driver");
    System.out.println("Driver loaded");
    Connection connection = DriverManager.getConnection
            ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
    System.out.println("Database connected");//Create a statement
    Statement stmt = connection.createStatement();
    String sql = "select id,name from student where sex = '女'";
        ResultSet rset = stmt.executeQuery(sql);
        while (rset.next()){
            out.println("id=" + rset.getString(1));
            out.println("name=" + rset.getString(2));
            out.println("sex=" + rset.getString(3));
            String id = rset.getString("id");
            String name = rset.getString("name");
            out.println(id + " " + name + "<br>");
        }
        rset.close();
        stmt.close();
        connection.close();}
    catch (SQLException e){
    e.printStackTrace();
    }
```



##### 4.改

```java
try {
        Class.forName("com.mysql.cj.jdbc.Driver");
        System.out.println("Driver loaded");
        Connection connection = DriverManager.getConnection
                ("jdbc:mysql://localhost:3306/javaweb-test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT", "root", "1328910");
        String sql = "update student set sex = '女' where id = '002';";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        int i = preparedStatement.executeUpdate();
        if (i > 0) {
            out.println("成功修改" + i + "行");
        }
        preparedStatement.close();
        connection.close();}
    catch (SQLException e){
        e.printStackTrace();
    }
```

##### 5.preparestatment版

```java
public ArrayList getListByCid(String cid) throws SQLException, ClassNotFoundException {
        ArrayList scores = new ArrayList();
        try{
            init();
            String sql = "select score.id,student.name,score.type,score.scorenum from score,student where  score.id = student.id and score.cid = ?";
            PreparedStatement preparedStatement = connection.prepareStatement(sql);
            preparedStatement.setString(1, cid);
//            Statement preparedStatement = connection.createStatement();
            ResultSet rset = preparedStatement.executeQuery();
            while (rset.next()) {
                Score score = new Score();
                score.setId(rset.getString("id"));
                score.setType(rset.getString("type"));
                score.setName(rset.getString("name"));
                score.setScorenum(rset.getString("scorenum"));
                scores.add(score);
            }
            rset.close();
            preparedStatement.close();
            connection.close();
        } catch (SQLException e){
        e.printStackTrace();
        }
        return scores;
    }

    public int updateScoes(String cid,String[] type,String[] id,String[] score) throws SQLException, ClassNotFoundException {
        int num = 0;
        init();
        String sql = "update score set scorenum = ? where id = ? and type = ? and cid = ?;";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for (int i = 0; i < id.length; i++) {
            preparedStatement.setString(1, score[i]);
            preparedStatement.setString(2, id[i]);
            preparedStatement.setString(3, type[i]);
            preparedStatement.setString(4, cid);
            preparedStatement.executeUpdate();
            num++;
        }
        preparedStatement.close();
        connection.close();
        return num;
    }
```

##### 6.关于空指针问题

很显然nmd注意创建的对象不要自己弄成Null！！！！！

弄成null了还set个鬼！！！
