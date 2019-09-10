---
layout: post
title:  "ContentValues_analysis"
date:   2019-09-11
image:
comments: true
---
ContentValues.java 분석
====================================

지난 주에 교수님께서 말씀해 주셨던 ContentValues 내의 인스턴스 별로 오버로딩 되어 있는 put 함수를 분석해보려고 합니다.  

우선 ContentValues의 역할을 찾아봤습니다.  

![image](https://user-images.githubusercontent.com/38609712/64600408-058c3100-d3f6-11e9-8b5c-b9238f098487.png)  

![image](https://user-images.githubusercontent.com/38609712/64600435-150b7a00-d3f6-11e9-94db-a673d23e088f.png)  

![image](https://user-images.githubusercontent.com/38609712/64600484-29e80d80-d3f6-11e9-987c-b9700e4aea09.png)  

출처:https://choidev-1.tistory.com/57  
위와 같이 ContentValues 클래스는 ContentResolver가 사용하기 위한 값의 집합입니다. 즉 ContentResolver가 사용하는 값들을 담은 객체라고 이해했습니다. 이는 DB에 삽입,수정,삭제 등의 작업을 할 때 사용하기 위한 값들을 객체형태로 사용하기 위해 ContentValues라는 클래스를 사용한다고 확인했습니다.  

저희가 다루려고하는 부분의 소스 코드를 보면


ContentProviderOperation.java 내의 withValue 함수

```

private final ContentValues mValues;

public Builder withValue(String key, Object value) {
            if (mType != TYPE_INSERT && mType != TYPE_UPDATE && mType != TYPE_ASSERT) {
                throw new IllegalArgumentException("only inserts and updates can have values");
            }
            if (mValues == null) {
                mValues = new ContentValues();
            }
            if (value == null) {
                mValues.putNull(key);
            } else if (value instanceof String) {
                mValues.put(key, (String) value);
            } else if (value instanceof Byte) {
                mValues.put(key, (Byte) value);
            } else if (value instanceof Short) {
                mValues.put(key, (Short) value);
            } else if (value instanceof Integer) {
                mValues.put(key, (Integer) value);
            } else if (value instanceof Long) {
                mValues.put(key, (Long) value);
            } else if (value instanceof Float) {
                mValues.put(key, (Float) value);
            } else if (value instanceof Double) {
                mValues.put(key, (Double) value);
            } else if (value instanceof Boolean) {
                mValues.put(key, (Boolean) value);
            } else if (value instanceof byte[]) {
                mValues.put(key, (byte[]) value);
            } else {
                throw new IllegalArgumentException("bad value type: " + value.getClass().getName());
            }
            return this;
        }

```  

위의 함수에서 ContentValues 타입의 mValues 의 put 함수를 인스턴스별로 호출하는 것을 확인 할 수 있습니다. 이에 따라 ContentValues의 클래스를 확인해 봤습니다.

ContentValues.java

```

private HashMap<String, Object> mValues;

 public void put(String key, String value) {
        mValues.put(key, value);
    }

    /**
     * Adds all values from the passed in ContentValues.
     *
     * @param other the ContentValues from which to copy
     */
    public void putAll(ContentValues other) {
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
     * Adds a null value to the set.
     *
     * @param key the name of the value to make null
     */
    public void putNull(String key) {
        mValues.put(key, null);
    }

```

위와 같이 ContentOperation에서 ContentValues 타입의 mValues에 put을 호출해서 사용하는데 실제 선언되어 있는 부분을 확인해봤습니다.
HashMap<String,Object> 의 형태로 선언된 mValues에 put을 하여 저장 하는 것으로 확인 할 수 있었습니다. 위의 put 함수들이 인스턴스 별로 쪼개어져 오버로딩 되어 있는 이유는 아직 찾지 못했습니다. 그러나 HashMap을 직접 테스팅 해본결과 HashMap<String,Object> 형태로 선언된 해시맵에 값으로 String,Byte,Short,Long 등의 어떠한 형태든 상관없이 put(String,Object) 형태의 함수 단 하나로 충분히 사용가능한 것을 확인했습니다. 따라서 위의 코드에서 인스턴스 별로 오버로딩 되어 있는 put 함수들을 없에고 Object타입의 put 함수 하나만 남겨서 빌드하여 문제 없이 돌아갈 경우에 Commit을 할만한 충분한 이유가 되지 않을까 생각 됩니다.  

또한 위의 코드가 안드로이드 버젼 9(파이) 까지는 계속 유지되었습니다.  
하지만 현재 개발 중인 안드로이드 버젼 10 (Q)에서는 변경 사항을 확인했습니다.  

ContentProviderOperation.java 내의 withValue 함수

```

public Builder withValue(String key, Object value) {
            if (mType != TYPE_INSERT && mType != TYPE_UPDATE && mType != TYPE_ASSERT) {
                throw new IllegalArgumentException("only inserts and updates can have values");
            }
            if (mValues == null) {
                mValues = new ContentValues();
            }
            if (value == null) {
                mValues.putNull(key);
            } else if (value instanceof String) {
                mValues.put(key, (String) value);
            } else if (value instanceof Byte) {
                mValues.put(key, (Byte) value);
            } else if (value instanceof Short) {
                mValues.put(key, (Short) value);
            } else if (value instanceof Integer) {
                mValues.put(key, (Integer) value);
            } else if (value instanceof Long) {
                mValues.put(key, (Long) value);
            } else if (value instanceof Float) {
                mValues.put(key, (Float) value);
            } else if (value instanceof Double) {
                mValues.put(key, (Double) value);
            } else if (value instanceof Boolean) {
                mValues.put(key, (Boolean) value);
            } else if (value instanceof byte[]) {
                mValues.put(key, (byte[]) value);
            } else {
                throw new IllegalArgumentException("bad value type: " + value.getClass().getName());
            }
            return this;
        }

```

우선 ContentProvierOperation의 WithValue 함수 자체에는 차이점이 존재하지 않습니다.  

```

private final ArrayMap<String, Object> mMap;


 public void put(String key, String value) {
        mMap.put(key, value);
    }

    /**
     * Adds all values from the passed in ContentValues.
     *
     * @param other the ContentValues from which to copy
     */
    public void putAll(ContentValues other) {
        mMap.putAll(other.mMap);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Byte value) {
        mMap.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Short value) {
        mMap.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Integer value) {
        mMap.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Long value) {
        mMap.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Float value) {
        mMap.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Double value) {
        mMap.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, Boolean value) {
        mMap.put(key, value);
    }

    /**
     * Adds a value to the set.
     *
     * @param key the name of the value to put
     * @param value the data for the value to put
     */
    public void put(String key, byte[] value) {
        mMap.put(key, value);
    }

    /**
     * Adds a null value to the set.
     *
     * @param key the name of the value to make null
     */
    public void putNull(String key) {
        mMap.put(key, null);
    }

```


안드로이드 버젼10 (Q)에서는 위와 같이 mValues에 대한 자료형이 ArrayList<String,Object> 형태로 변경되었으나, 인스턴스 별로 나뉘어 있는 put 함수에 대한 변경사항은 없었다.  

위 변경점에 대한 커밋은 " Developers often accept selection clauses from untrusted code, and
SQLiteQueryBuilder already supports a "strict" mode to help catch
SQL injection attacks.  This change extends the builder to support
update() and delete() calls, so that we can help secure those
selection clauses too.

Extend it to support selection arguments being provided when
appending appendWhere() clauses, meaning developers no longer need
to manually track their local selection arguments along with
remote arguments.

Extend it to support newer ContentProvider.query() variant that
accepts "Bundle queryArgs", and have all query() callers flow
through that common code path.  (This paves the way for a future
CL that will offer to gracefully extract non-WHERE clauses that
callers have tried smashing into their selections.)

Updates ContentValues to internally use more efficient ArrayMap. "

로 Extend SQLiteQueryBuilder for update and delete 를 위한 Commit,Merge 중 ArrayList가 더 효율적이여서 ContentValues에서 mValues의 자료형을 변경한 것을 확인했다. 그러나 put 함수의 오버로딩에 대한 변경 사항은 없었다. 인스턴스별로 나누어져 있는 이유는 아직 찾지 못했으나 나눠져 있는 put 함수들을 하나로 합쳐서 빌드와 성능테스트를 시도 중입니다.
