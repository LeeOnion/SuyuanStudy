# Android 项目

该文档主要介绍常用的一些代码功能

***

### 1 Acvivity数据传输

本项目采用intent进行数据传输。如下述代码所示，我们实现了A跳转到B的功能，并利用intent.putExtra实现数据传输。

A端：

```java
Intent intent = new Intent();
intent.setClass(A.this,B.class);
intent.putExtra("username",username);
A.this.startActivity(intent);
```

B端：

```java
Intent intent = getIntent();
username= intent.getStringExtra("username");
```

### 2 发送和处理Http协议

如下述代码所示，第一个方法实现了Http请求的发送，以及回传数据的接受。最后通过调用handle方法实现了数据的处理。

需要注意的是，我们无法在子线程修改UI，因此需要用handle实现。

```
    private void GetProduct(final int productserial){
        // 开启线程，发送请求
        new Thread(new Runnable() {
            @Override
            public void run() {
                HttpURLConnection connection = null;
                BufferedReader reader = null;
                urltext = "xxxxxx" // http的url
                try {
                    URL url = new URL(urltext);
                    connection = (HttpURLConnection) url.openConnection();
                    connection.setRequestMethod("GET");
                    connection.setConnectTimeout(5000);
                    connection.setReadTimeout(5000);
                    InputStream in = connection.getInputStream();
                    reader = new BufferedReader(new InputStreamReader(in));
                    StringBuilder result = new StringBuilder();
                    String line;
                    while ((line = reader.readLine()) != null) {
                        result.append(line);
                    }
                    handle(result.toString());
                } catch (Exception e) {
                } finally {
                    if (reader != null) {
                        try {
                            reader.close();
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }
                    if (connection != null) {
                        connection.disconnect();
                    }
                }
            }
        }).start();
    }

    public void handle(final String result) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                try {
                    JSONObject item = new JSONObject(result);
                    // do something
                } catch (JSONException e) {
                    e.printStackTrace();
                }
            }
        });
    }
```

### 3 NFC功能

这里我们以MainActivity实现NFC为例：

- 首先我们必须确保已经在AndroidMainfest.xml中开启NFC权限，以及读写权限。

- 书写BaseNfcActivity（项目中已经写好）
- MainActivity继承BaseNfcActivity

```java
	// 重写onNewIntent方法
	@Override
    // 事件响应
    public void onNewIntent(Intent intent) {
        final Tag tag = intent.getParcelableExtra(NfcAdapter.EXTRA_TAG);
        String[] techList = tag.getTechList();
        boolean haveMifareUltralight = false;
        for (String tech : techList) {
            if (tech.indexOf("MifareUltralight") >= 0) {
                haveMifareUltralight = true;
                break;
            }
        }
        if (!haveMifareUltralight) {
        	// 不支持NFC
            return;
        }
        final MifareUltralight ultralight = MifareUltralight.get(tag);
        // 读标签
        readTag(ultralight);
        // 写标签
        writeTag(ultralight);
    }
    // 读标签
    public String readTag(MifareUltralight ultralight) {

        try {
            ultralight.connect();
            byte[] data1 = ultralight.readPages(4);
            byte[] data2 = ultralight.readPages(8);
            for (int i = 0; i < 4; i++) {
                plantseriall[i] = data1[i];
                processseriall[i] = data1[i + 4];
                transferseriall[i] = data1[i + 8];
                saleseriall[i] = data1[i + 12];
                productseriall[i] = data2[i];
            }
            plantstr = MyUtils.toDec(plantseriall);
            processstr = MyUtils.toDec(processseriall);
            transferstr = MyUtils.toDec(transferseriall);
            salestr = MyUtils.toDec(saleseriall);
            productstr = MyUtils.toDec(productseriall);
            return new String(data1);
        } catch (Exception e) {

        } finally {
            try {
                ultralight.close();
            } catch (Exception e) {

            }
        }
        return null;
    }
    // 写标签
    public void writeTag(MifareUltralight ultralight) {
        try {
            ultralight.connect();
            if (kind == 1) {
                // 写Page4：plantserial和Page8 ProductSerial
                byte[] data1 = MyUtils.toHex(plantserial);
                byte[] data2 = MyUtils.toHex(productserial);
                ultralight.writePage(4, data1);
                ultralight.writePage(8, data2);
                Toast.makeText(this, "环节一写入成功", Toast.LENGTH_SHORT).show();
            } else if (kind == 2) {
                // 写Page5
                byte[] data = MyUtils.toHex(processserial);
                ultralight.writePage(5, data);
                Toast.makeText(this, "环节二写入成功", Toast.LENGTH_SHORT).show();
            } else if (kind == 3) {
                // 写Page6
                byte[] data = MyUtils.toHex(transferserial);
                ultralight.writePage(6, data);
                Toast.makeText(this, "环节三写入成功", Toast.LENGTH_SHORT).show();
            } else if (kind == 4) {
                // 写Page7
                byte[] data = MyUtils.toHex(saleserial);
                ultralight.writePage(7, data);
                Toast.makeText(this, "环节四写入成功", Toast.LENGTH_SHORT).show();
            } else {
                Toast.makeText(this, "未知阶段", Toast.LENGTH_SHORT).show();
            }
            showSingleAlertDialog();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                ultralight.close();
            } catch (Exception e) {
            }
        }
    }
```

### 4 TODO