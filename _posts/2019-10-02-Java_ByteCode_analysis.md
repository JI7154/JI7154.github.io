---
layout: post
title:  "Unit Test"
date:   2019-09-17
image:
comments: true
---
Java Byte Code Analysis
====================================
지금까지 개선해 왔던 코드가 조금 더 확실하게 안전한지 검증해 보기 위해 Java Byte 코드를 이용하여 확인해 보았다.
우선 바이트코드 확인을 위한 단위테스트에서 사용한 코드들은 다음과 같다.

```

//개선하기 이전의 ContentProviderOperation.java 의 withValue 함수
public static void withValueBefore(String key, Object value) {

        if (mType != TYPE_INSERT && mType != TYPE_UPDATE && mType != TYPE_ASSERT) {
            throw new IllegalArgumentException("only inserts and updates can have values");
        }
        if (mValuesBefore == null) {
            mValuesBefore = new ContentValuesBefore();
        }
        if (value == null) {
            mValuesBefore.putNull(key);
        } else if (value instanceof String) {
            mValuesBefore.put(key, (String) value);
        } else if (value instanceof Byte) {
            mValuesBefore.put(key, (Byte) value);
        } else if (value instanceof Short) {
            mValuesBefore.put(key, (Short) value);
        } else if (value instanceof Integer) {
            mValuesBefore.put(key, (Integer) value);
        } else if (value instanceof Long) {
            mValuesBefore.put(key, (Long) value);
        } else if (value instanceof Float) {
            mValuesBefore.put(key, (Float) value);
        } else if (value instanceof Double) {
            mValuesBefore.put(key, (Double) value);
        } else if (value instanceof Boolean) {
            mValuesBefore.put(key, (Boolean) value);
        } else if (value instanceof byte[]) {
            mValuesBefore.put(key, (byte[]) value);
        } else {
            throw new IllegalArgumentException("bad value type: " + value.getClass().getName());
        }

    }


//개선한 이후의 withValue 함수

	public static void withValue(String key, Object value) {

        if (mType != TYPE_INSERT && mType != TYPE_UPDATE && mType != TYPE_ASSERT) {
            throw new IllegalArgumentException("only inserts and updates can have values");
        }
        if (mValues == null) {
            mValues = new ContentValues();
        }
        if (value == null) {
            mValues.putNull(key);
        }
        else if(value !=null) {
        	mValues.put(key,value);
        }
        else {
            throw new IllegalArgumentException("bad value type: " + value.getClass().getName());
        }

    }

//개선하기 이전의 ContentValues의 코드

private HashMap<String, Object> mValues;

    public ContentValuesBefore() {
    	mValues = new HashMap<String,Object>();
    }

	public void put(String key, String value) {
        mValues.put(key, value);
    }

    /**
     * Adds all values from the passed in ContentValues.
     *
     * @param other the ContentValues from which to copy
     */
    public void putAll(ContentValuesBefore other) {
        mValues.putAll(other.mValues);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Byte value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Short value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Integer value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Long value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Float value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Double value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Boolean value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, byte[] value) {
        mValues.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */

    public void put(String key, Object value) {
        mValues.put(key, value);
    }

    /**
     * Adds a null value to the set.
     *
     * @param key the name of the value to make null
     */
    public void putNull(String key) {
        mValues.put(key, null);
    }

//개선한 이후의 ContentValues 코드
private HashMap<String, Object> mValues;

    public ContentValues() {
    	mValues = new HashMap<String,Object>();
    }
    public void put(String key, Object value) {
        mValues.put(key, value);
    }
    public void putAll(ContentValues other) {
        mValues.putAll(other.mValues);
    }

    public void putNull(String key) {
        mValues.put(key, null);
    }

```

동작 과정은 withValue 함수에서 ContentValues에 존재하는 put 함수를 호출하면 ContentValues에 존재한 HashMap<String,Object> 형태의 자료구조에 key,value를 저장하는 형태이다.

```

// 개선전 withValue 의 바이트 코드
public static void withValueBefore(java.lang.String, java.lang.Object);
    Code:
       0: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
       3: ifnonnull     16
       6: new           #31                 // class ContentValuesBefore
       9: dup
      10: invokespecial #33                 // Method ContentValuesBefore."<init>":()V
      13: putstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
      16: aload_1
      17: ifnonnull     30
      20: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
      23: aload_0
      24: invokevirtual #34                 // Method ContentValuesBefore.putNull:(Ljava/lang/String;)V
      27: goto          249
      30: aload_1
      31: instanceof    #38                 // class java/lang/String
      34: ifeq          51
      37: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
      40: aload_0
      41: aload_1
      42: checkcast     #38                 // class java/lang/String
      45: invokevirtual #40                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/String;)V
      48: goto          249
      51: aload_1
      52: instanceof    #44                 // class java/lang/Byte
      55: ifeq          72
      58: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
      61: aload_0
      62: aload_1
      63: checkcast     #44                 // class java/lang/Byte
      66: invokevirtual #46                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/Byte;)V
      69: goto          249
      72: aload_1
      73: instanceof    #49                 // class java/lang/Short
      76: ifeq          93
      79: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
      82: aload_0
      83: aload_1
      84: checkcast     #49                 // class java/lang/Short
      87: invokevirtual #51                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/Short;)V
      90: goto          249
      93: aload_1
      94: instanceof    #54                 // class java/lang/Integer
      97: ifeq          114
     100: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
     103: aload_0
     104: aload_1
     105: checkcast     #54                 // class java/lang/Integer
     108: invokevirtual #56                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/Integer;)V
     111: goto          249
     114: aload_1
     115: instanceof    #59                 // class java/lang/Long
     118: ifeq          135
     121: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
     124: aload_0
     125: aload_1
     126: checkcast     #59                 // class java/lang/Long
     129: invokevirtual #61                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/Long;)V
     132: goto          249
     135: aload_1
     136: instanceof    #64                 // class java/lang/Float
     139: ifeq          156
     142: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
     145: aload_0
     146: aload_1
     147: checkcast     #64                 // class java/lang/Float
     150: invokevirtual #66                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/Float;)V
     153: goto          249
     156: aload_1
     157: instanceof    #69                 // class java/lang/Double
     160: ifeq          177
     163: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
     166: aload_0
     167: aload_1
     168: checkcast     #69                 // class java/lang/Double
     171: invokevirtual #71                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/Double;)V
     174: goto          249
     177: aload_1
     178: instanceof    #74                 // class java/lang/Boolean
     181: ifeq          198
     184: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
     187: aload_0
     188: aload_1
     189: checkcast     #74                 // class java/lang/Boolean
     192: invokevirtual #76                 // Method ContentValuesBefore.put:(Ljava/lang/String;Ljava/lang/Boolean;)V
     195: goto          249
     198: aload_1
     199: instanceof    #79                 // class "[B"
     202: ifeq          219
     205: getstatic     #29                 // Field mValuesBefore:LContentValuesBefore;
     208: aload_0
     209: aload_1
     210: checkcast     #79                 // class "[B"
     213: invokevirtual #81                 // Method ContentValuesBefore.put:(Ljava/lang/String;[B)V
     216: goto          249
     219: new           #84                 // class java/lang/IllegalArgumentException
     222: dup
     223: new           #86                 // class java/lang/StringBuilder
     226: dup
     227: ldc           #88                 // String bad value type:
     229: invokespecial #90                 // Method java/lang/StringBuilder."<init>":(Ljava/lang/String;)V
     232: aload_1
     233: invokevirtual #92                 // Method java/lang/Object.getClass:()Ljava/lang/Class;
     236: invokevirtual #96                 // Method java/lang/Class.getName:()Ljava/lang/String;
     239: invokevirtual #102                // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
     242: invokevirtual #106                // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
     245: invokespecial #109                // Method java/lang/IllegalArgumentException."<init>":(Ljava/lang/String;)V
     248: athrow
     249: return

//개선 후 withValue의 바이트 코드
public static void withValue(java.lang.String, java.lang.Object);
    Code:
       0: getstatic     #118                // Field mValues:LContentValues;
       3: ifnonnull     16
       6: new           #120                // class ContentValues
       9: dup
      10: invokespecial #122                // Method ContentValues."<init>":()V
      13: putstatic     #118                // Field mValues:LContentValues;
      16: aload_1
      17: ifnonnull     30
      20: getstatic     #118                // Field mValues:LContentValues;
      23: aload_0
      24: invokevirtual #123                // Method ContentValues.putNull:(Ljava/lang/String;)V
      27: goto          75
      30: aload_1
      31: ifnull        45
      34: getstatic     #118                // Field mValues:LContentValues;
      37: aload_0
      38: aload_1
      39: invokevirtual #124                // Method ContentValues.put:(Ljava/lang/String;Ljava/lang/Object;)V
      42: goto          75
      45: new           #86                 // class java/lang/IllegalArgumentException
      48: dup
      49: new           #88                 // class java/lang/StringBuilder
      52: dup
      53: ldc           #90                 // String bad value type:
      55: invokespecial #92                 // Method java/lang/StringBuilder."<init>":(Ljava/lang/String;)V
      58: aload_1
      59: invokevirtual #94                 // Method java/lang/Object.getClass:()Ljava/lang/Class;
      62: invokevirtual #98                 // Method java/lang/Class.getName:()Ljava/lang/String;
      65: invokevirtual #104                // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      68: invokevirtual #108                // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      71: invokespecial #111                // Method java/lang/IllegalArgumentException."<init>":(Ljava/lang/String;)V
      74: athrow
      75: return

```

위의 바이트코드를 보면 ContentValues의 put함수를 호출하며 넘겨주는 파라미터에서 이전의 코드는 캐스팅후에 넘겨주고 개선 후 코드는 Object 형태로 넘기는 것을 알 수 있다.

```

//개선전 ContentValues의 바이트코드
public class ContentValuesBefore {
  public ContentValuesBefore();
    Code:
       0: aload_0
       1: invokespecial #12                 // Method java/lang/Object."<init>":()V
       4: aload_0
       5: new           #14                 // class java/util/HashMap
       8: dup
       9: invokespecial #16                 // Method java/util/HashMap."<init>":()V
      12: putfield      #17                 // Field mValues:Ljava/util/HashMap;
      15: return

  public void put(java.lang.String, java.lang.String);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void putAll(ContentValuesBefore);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       8: invokevirtual #33                 // Method java/util/HashMap.putAll:(Ljava/util/Map;)V
      11: return

  public void put(java.lang.String, java.lang.Byte);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, java.lang.Short);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, java.lang.Integer);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, java.lang.Long);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, java.lang.Float);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, java.lang.Double);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, java.lang.Boolean);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, byte[]);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void put(java.lang.String, java.lang.Object);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void putNull(java.lang.String);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aconst_null
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return
}

//개선후 ContentValues의 바이트 코드
public class ContentValues {
  public ContentValues();
    Code:
       0: aload_0
       1: invokespecial #12                 // Method java/lang/Object."<init>":()V
       4: aload_0
       5: new           #14                 // class java/util/HashMap
       8: dup
       9: invokespecial #16                 // Method java/util/HashMap."<init>":()V
      12: putfield      #17                 // Field mValues:Ljava/util/HashMap;
      15: return

  public void put(java.lang.String, java.lang.Object);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aload_2
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return

  public void putAll(ContentValues);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       8: invokevirtual #34                 // Method java/util/HashMap.putAll:(Ljava/util/Map;)V
      11: return

  public void putNull(java.lang.String);
    Code:
       0: aload_0
       1: getfield      #17                 // Field mValues:Ljava/util/HashMap;
       4: aload_1
       5: aconst_null
       6: invokevirtual #25                 // Method java/util/HashMap.put:(Ljava/lang/Object;Ljava/lang/Object;)Ljava/lang/Object;
       9: pop
      10: return
}


```

위의 바이트코드를 확인해 보면 결국 인스턴스 별로 나누어져 있는 put함수들도 결국 HashMap<String,Object> 의 자료구조에 put하기 위하여 결국 Key,value를 Object 형태로 저장하는 것을 알 수가 있다. 즉 결국 withValue에서 캐스팅하여 넘어와도 오히려 Object형태로 저장하기 때문에 인스턴스별로 나눠져 넘기고 저장하는 의미가 없다는 것을 확인할 수 있으며, 오히려 withValue함수에서 narrow casting을 하기때문에 불필요한 overHead가 발생할 가능성이 존재함을 확인 할 수 있다.