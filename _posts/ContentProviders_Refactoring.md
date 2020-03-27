---

layout: post
title:  "ContentProviders Refactoring"
date:   2020-03-27
comments: false

---

# ContentProviders Refactoring


## #ContentProviderOperation.java(before refactoring)![enter image description here](https://k.kakaocdn.net/dn/dMB7Cn/btqCLMdGGBj/b2RgaibxqIlGWtKMf23L2K/img.png)


## ContentValues.java(before refactoring)
![enter image description here](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https://k.kakaocdn.net/dn/cuxRuJ/btqCMnLrZJH/ukPzSkCPl3BY9HSRkppB30/img.png)
![enter image description here](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https://k.kakaocdn.net/dn/eUW0ry/btqCKf8JrbO/bBkarrI4WKyP2Y4jXyPNHK/img.png)
This are two classes in content Package. withValue function in ContentProviderOperation calls put functions in ContentValues.java. There are unnecessarily duplicate type check. It causes overhead and decreses readability.

## Bytecode of put functions in ContentValues.java

![You can rename the current file by clicking the file name in the navigation bar or by clicking the **Rename** button in the file explorer.](https://k.kakaocdn.net/dn/cs07uW/btqC0KzFZJh/XhBGXUkiqrZrgjsInP5U0K/img.png)
Android compiles the source code at runtime and runs it as bytecode.  If you look at the bytecode above, you can see that they all use the same type. It can be used regardless of type and does not cause an error. However, AOSP(Android Open Source Project) values type safety. Thus,  Refactoring was performed while keeping type safety. Also, It reduces the use of 'instanceof' of withValue function in ContentProvidersOperation.java  and reduces the size of bytecode.

## After Refactoring
 ContentProviderOperation.java

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
					mValues.put(key, value);
				} catch (IllegalArgumentException e) {
					throw new IllegalArgumentException("bad value type: " + value.getClass().getName());
				}
			}
			return this;
		}

ContentValues.java

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, String value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Byte value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Short value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Integer value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Long value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Float value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Double value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Boolean value) {
		mMap.put(key, value);
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, byte[] value) {
		mMap.put(key, value);
	}
            
    /**
	 * Checks a value belongs to proper type group
	 * 
	 * @param value
	 *            the data for the value to type check
	 * @return {@code true} if the value belongs proper type, {@code false}
	 *         otherwise
	 */
	public boolean isProperTypes(Object value) {
		return value instanceof String || value instanceof Byte || value instanceof Short || value instanceof Integer
				|| value instanceof Long || value instanceof Float || value instanceof Double
				|| value instanceof Boolean || value instanceof byte[];
	}

	/**
	 * Adds a value to the set.
	 *
	 * @param key
	 *            the name of the value to put
	 * @param value
	 *            the data for the value to put
	 */
	public void put(String key, Object value) throws IllegalArgumentException {
		if (isProperTypes(value)) {
			mMap.put(key, value);
		} else {
			throw new IllegalArgumentException();
		}
	}

  
You can see many examples of using the withValue function in Programcreek.
URL : [https://www.programcreek.com/java-api-examples/?class=android.content.ContentProviderOperation.Builder&method=withValue](https://www.programcreek.com/java-api-examples/?class=android.content.ContentProviderOperation.Builder&method=withValue)
