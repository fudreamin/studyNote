# Java深浅拷贝

- 浅拷贝只是拷贝了源对象的地址，所以源对象的值发生变化时，拷贝对象的值也会发生变化。

![](https://pic3.zhimg.com/v2-82df93065af4907d20042b24a28aa638_r.jpg)

## 1、

```java
package niuke;

public class Copy {
    public static void main(String[] args) {
        Address address = new Address("杭州", "中国");
        User user = new User("大山", address);

        // 调用构造函数时进行深拷贝
        User copyUser = new User(user.getName(), new Address(address.getCity(), address.getCountry()));

        // 修改源对象的值
        user.getAddress().setCity("深圳");

        // 检查两个对象的值不同
        System.out.println(user.getAddress().getCity()+"\n"+ copyUser.getAddress().getCity());
    }
}

/**
 * 用户
 */
class User {

    private String name;
    private Address address;

    // constructors, getters and setters

    public User(String name, Address address) {
        this.name = name;
        this.address = address;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Address getAddress() {
        return address;
    }

    public void setAddress(Address address) {
        this.address = address;
    }
}

/**
 * 地址
 */
class Address {

    private String city;
    private String country;
    
    @Override
    public Address clone() throws CloneNotSupportedException {
        return (Address) super.clone();
    }

    // constructors, getters and setters


    public Address(String city, String country) {
        this.city = city;
        this.country = country;
    }

    public String getCity() {
        return city;
    }

    public void setCity(String city) {
        this.city = city;
    }

    public String getCountry() {
        return country;
    }

    public void setCountry(String country) {
        this.country = country;
    }
}
```

