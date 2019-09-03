---
layout: post
title:  "enumMap 성능 분석"
date:   2019-08-29
image:
comments: true
---
Code Refactoring
================================
이전 시간에 활용해서 만들었던 enumMap을 이용한 코드 리팩토링이 완전하지 않으며 허접이 많다는 피드백을 받아 해당 코드를 다시 refactoring을 시도 해봤습니다. 우선 enum과 enumMap에 대한 개념 확립을 다시 해봤습니다.

Java enum 정리
- 1 . 클래스의 멤버로 사용
```
public enum Currency {PENNY, NICKLE, DIME, QUARTER};

Currency coin = Currency.PENNY;

coin = 1; //compilation error

```

- 2  클래스의 멤버로 사용 (값을 지정)
  
```
public enum Currency {PENNY(1), NICKLE(5), DIME(10), QUARTER(25)};
//The constructor Type(int) is undefined 에러 발생
```

- 3 switch 문의 인자로 사용
```
Currency usCoin = Currency.DIME;
    switch (usCoin) {
            case PENNY:
                    System.out.println("Penny coin");
                    break;
            case NICKLE:
                    System.out.println("Nickle coin");
                    break;
            case DIME:
                    System.out.println("Dime coin");
                    break;
            case QUARTER:
                    System.out.println("Quarter coin");
    }
```

- 4 enum 안에 정의된 상수들은 final 이라, == 으로 비교가능
```
Currency usCoin = Currency.DIME;
    if(usCoin == Currency.DIME){
       System.out.println("enum in java can be"+
               "compared using ==");
    }
```

- 5 메소드 확장하기
```
public enum SizeEnum {

  SMALL("S"), MEDIUM("M"), LARGE("L");

  // Fields

  private String mAbbreviation;

  // Constructor

  private SizeEnum(String abbreviation) {

   mAbbreviation = abbreviation;

  }

   

  // Methods

  public String getAbbreviation() { return mAbbreviation; }

  public void setAbbreviation(String abbreviation) { mAbbreviation = abbreviation; }

 

}

```

- 6 Enum 클래스로 사용하기
```
public enum FontStyle {
    NORMAL, BOLD, ITALIC, UNDERLINE;
 
    FontStyle() {
    }
}

인라인 클래스로 사용  ( sample.FontStyle.NORMAL  접근) 

public enum SampleClass {
    public enum FontStyle { NORMAL, BOLD, ITALIC, UNDERLINE }
    ...
}
```

- 7 EunmSet 사용하기
```
import java.util.EnumSet;

import java.util.Set;

/**
 * Simple Java Program to demonstrate how to use EnumSet.
 * It has some interesting use cases and it's specialized collection for
 * Enumeration types. Using Enum with EnumSet will give you far better
 * performance than using Enum with HashSet, or LinkedHashSet.
 *
 * @author Javin Paul
 */
public class EnumSetDemo {

    private enum Color {
        RED(255, 0, 0), GREEN(0, 255, 0), BLUE(0, 0, 255);
        private int r;
        private int g;
        private int b;
        private Color(int r, int g, int b) {
            this.r = r;
            this.g = g;
            this.b = b;
        }
        public int getR() {
            return r;
        }
        public int getG() {
            return g;
        }
        public int getB() {
            return b;
        }
    }


    public static void main(String args[]) {
        // this will draw line in yellow color
        EnumSet<Color> yellow = EnumSet.of(Color.RED, Color.GREEN);
        drawLine(yellow);
        // RED + GREEN + BLUE = WHITE
        EnumSet<Color> white = EnumSet.of(Color.RED, Color.GREEN, Color.BLUE);
        drawLine(white);
        // RED + BLUE = PINK
        EnumSet<Color> pink = EnumSet.of(Color.RED, Color.BLUE);
        drawLine(pink);
    }


    public static void drawLine(Set<Color> colors) {
        System.out.println("Requested Colors to draw lines : " + colors);
        for (Color c : colors) {
            System.out.println("drawing line in color : " + c);
        }
    }
}

Output:
Requested Colors to draw lines : [RED, GREEN]
drawing line in color : RED
drawing line in color : GREEN

Requested Colors to draw lines : [RED, GREEN, BLUE]
drawing line in color : RED
drawing line in color : GREEN
drawing line in color : BLUE

Requested Colors to draw lines : [RED, BLUE]
drawing line in color : RED
drawing line in color : BLUE
```

- 8 EnumSet으로 비트필드 대체하기
```
package resolver;

public class IntEnumPatternExample {

    public static final int STYLE_BOLD          = 1 << 0; // 1
    public static final int STYLE_ITALIC        = 1 << 1; // 2
    public static final int STYLE_UNDERLINE     = 1 << 2; // 4
    public static final int STYLE_STRIKETHROUGH = 1 << 3; // 8

    public static void main(String[] args) {
        final IntEnumPatternResolver resolver = new IntEnumPatternResolver();
        resolver.enableAll(STYLE_BOLD, STYLE_ITALIC, STYLE_STRIKETHROUGH, STYLE_UNDERLINE);
        resolver.disable(STYLE_STRIKETHROUGH);
        resolver.toggle(STYLE_UNDERLINE);
        print(resolver);
    }

    private static void print(IntEnumPatternResolver resolver) {
        assert resolver.isEnabled(STYLE_BOLD) == true;
        assert resolver.isEnabled(STYLE_ITALIC) == true;
        assert resolver.isEnabled(STYLE_UNDERLINE) == false;
        assert resolver.isEnabled(STYLE_STRIKETHROUGH) == false;

        System.out.println("STYLE_BOLD: " + resolver.isEnabled(STYLE_BOLD));
        System.out.println("STYLE_ITALIC: " + resolver.isEnabled(STYLE_ITALIC));
        System.out.println("STYLE_UNDERLINE: " + resolver.isEnabled(STYLE_UNDERLINE));
        System.out.println("STYLE_STRIKETHROUGH: " + resolver.isEnabled(STYLE_STRIKETHROUGH));
    }

}
package resolver;

import java.util.EnumSet;

public class EnumPatternExample {

    public enum Style {
        BOLD, ITALIC, UNDERLINE, STRIKETHROUGH
    }

    public static void main(String[] args) {
        final EnumSet<Style> styles = EnumSet.noneOf(Style.class);
        styles.addAll(EnumSet.range(Style.BOLD, Style.STRIKETHROUGH)); // enable all constants
        styles.removeAll(EnumSet.of(Style.UNDERLINE, Style.STRIKETHROUGH)); // disable a couple
        assert EnumSet.of(Style.BOLD, Style.ITALIC).equals(styles); // check set contents are correct
        System.out.println(styles);
    }

}
```

- 9 EnumMap 사용하기
```
enum Importance {
Low, Medium, High, Critical
}


EnumMap<Importance, String> enumMap = new EnumMap<>(Importance.class);


enumMap.put(Importance.Low, "=Low");
enumMap.put(Importance.High, "=High");


String value1 = enumMap.get(Importance.Low);
String value2 = enumMap.get(Importance.High);


package program;
import java.util.EnumMap;
public class Program {
    // 1. Create an Enum.
    enum Importance {
Low, Medium, High, Critical
    }
    public static void main(String[] args) {
        // 2. Create an EnumMap.
        EnumMap<Importance, String> enumMap = new EnumMap<>(Importance.class);
        // 3. PUT values into the map.
        enumMap.put(Importance.Low, "=Low");
        enumMap.put(Importance.High, "=High");
        // 4. Get values from the map.
        String value1 = enumMap.get(Importance.Low);
        String value2 = enumMap.get(Importance.High);
        System.out.println(value1);
        System.out.println(value2);
    }
}
```

출처: https://hamait.tistory.com/383 [HAMA 블로그]

위와 같은 Enum의 사용법이 있어서 이해하고 시도해봤습니다.
하지만 저희가 필요한 withValue 함수에서는 Object 타입의 parameter를 받아서 타입 검사를 한 후 알맞은 형변환을 통해 mValues 라는 HashMap에 Push를 해야하는 상황입니다.  
 이에 따라 value라는 Object 타입의 자료형 검사가 필수적이 되어야 합니다. 자바에는 typeof 같이 primitive 자료형을 String형태로 돌려주는 함수가 없어 instanceOf를 사용해야하는데 이경우에는 원래 있던 형태에서 벗어날 수가 없어 성능개선의 여지가 없어 보여서 다른 방법을 시도하고 있습니다.  
 ```
 String.class.getName() // java.lang.String
 Byte.class.getName() // java.lang.Byte
 ...
 ```
 위와 같은 방식으로 getClass.getName()을 통한 자료형 비교를 하려고 시도하였으나 return 타입이 String임에도 불구하고 Enum 내부에 java.lang.String 같은 형태의 문자열을 선언하는 것이 불가능했습니다. 따라서 2번째 방법의 값을 같이 할당하는 형태로 String(java.lang.String) 으로 enum을 열거해 보았으나 역시 Constructor is undefined 에러가 발생했습니다.  
 위의 방법들이 진행이 안되어 getClass().getName()의 리턴타입이 String을 파싱하는 방법을 생각했습니다.  
 먼저 getName()으로 가져온 값들을 split("\\.")[2] 을 통해 자료형을 String 형태로 가져온 후 enum에 선언되어 있는 Types.String 과 같은 값을 == 으로 동등 비교를 시도해 보았으나 Types.String 같은 값들은 자료형이 Types여서 동등비교가 불가능했습니다. 그에 따라 Types의 값들을 (String) 형태로 casting을 시도했으나 안되었고 반대로 파싱한 문자열을 enum 형태로 casting하는 것도 잘 안되었습니다.  
 그래서 enum 타입의 Types에 존재하는 valueOf() 를 통해 valueOf(String)을 통해 Types 내부의 값을 가져와 enumMap에 Key 값으로 넣는 형태로 해서 다음과 같은 코드를 작성했습니다. 빌드 환경이 아직 구축이 안되서 리팩토링한 코드로 향후 빌드를 시도해보겠습니다.

 ```
private enum Types {
        String, Byte, Short, Integer, Long, Float, Double, Boolean;
    }

 public Builder withValue(String key, Object value) {

            if (mType != TYPE_INSERT && mType != TYPE_UPDATE && mType != TYPE_ASSERT) {
                throw new IllegalArgumentException("only inserts and updates can have values");
            }
            if (mValues == null) {
                mValues = new ContentValues();
            }
            if (value == null) {
                mValues.putNull(key);
            } else {
                try {
                    EnumMap<Types, Object> enumMap = new EnumMap<Types, Object>(Types.class);
                    enumMap.put(Types.String, (String) value);
                    enumMap.put(Types.Byte, (Byte) value);
                    enumMap.put(Types.Short, (Short) value);
                    enumMap.put(Types.Integer, (Integer) value);
                    enumMap.put(Types.Long, (Long) value);
                    enumMap.put(Types.Float, (Float) value);
                    enumMap.put(Types.Double, (Double) value);
                    enumMap.put(Types.Boolean, (Boolean) value);
                    mValues.put(key, enumMap.get(Types.valueOf(value.getClass().getName().split("\\.")[2])));
                }

//                    mValues.put(key,value.getClass().cast(value));
//                    HashMap<String,Object> valueTypeMap = new HashMap<String, Object>();
//                    valueTypeMap.put(String.class.getName(),(String)value);
//                    valueTypeMap.put(Byte.class.getName(),(Byte)value);
//                    valueTypeMap.put(Short.class.getName(),(Short)value);
//                    valueTypeMap.put(Integer.class.getName(),(Integer)value);
//                    valueTypeMap.put(Long.class.getName(),(Long)value);
//                    valueTypeMap.put(Float.class.getName(),(Float)value);
//                    valueTypeMap.put(Double.class.getName(),(Double)value);
//                    valueTypeMap.put(Boolean.class.getName(),(Boolean)value);
//                    valueTypeMap.put(byte[].class.getName(),(byte[])value);
//                    mValues.put(key,valueTypeMap.get(value.getClass().getName()));
                } catch (Exception e) {
                    throw new IllegalArgumentException("bad value type: " + value.getClass().getName());
                }
            
            return this;
        }
 ```

지난 주에 진행했던 enumMap을 다시 refactoring하여 Switch문을 없에고 문자열 파싱을 통해 enumMap으로 구현했습니다.
 
 