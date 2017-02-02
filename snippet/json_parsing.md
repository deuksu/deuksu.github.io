# json parsing :: com.fasterxml.jackson.* , net.sf.json.*(net.sf.json-lib)
<hr/>

### net.sf.json-lib
``` 

  /**
   * 데이타 받기
   * @method loadFullData
   * @see
   * @param param
   * @return
   * @throws Exception String
   */
  private static String loadFullData(String param) throws Exception{
    URL url = new URL(param);

    HttpURLConnection con = (HttpURLConnection) url.openConnection();
    con.setDoOutput(true);

    String doc = "";
    BufferedInputStream bis = null;
    InputStreamReader isr = null;
    BufferedReader br = null;

    StringBuilder sb = new StringBuilder();

    try {
      bis = new BufferedInputStream(con.getInputStream());
      isr = new InputStreamReader(bis, "UTF-8");
      br = new BufferedReader(isr);
  
      sb.setLength(0);
      String line = "";
      while((line = br.readLine()) != null) {
        sb.append(line);
      }
  
      doc = sb.toString();
      sb = null;
    } catch (Exception e) {
      e.printStackTrace();
    } finally {
      if(br != null) br.close();
      if(isr != null) isr.close();
      if(bis != null) bis.close();
    }

    if(con != null) con.disconnect();

    return doc;
  }


  /**
   * feed정보 조회 / 처리
   * @method updateGameList
   * @see
   * @throws Exception
   */
  @Transactional(rollbackFor=Exception.class)
  @Override
  public void updateGameList() throws Exception {
    String jsonData = loadFullData("http://...");
    JSONObject jsonObj = JSONObject.fromObject(jsonData);

    Iterator<?> itr = jsonObj.keys();
    while(itr.hasNext()){
      Object key = itr.next();
      ..
    }
  }

```
<hr/>

### fasterxml
``` 

  public Map<String, Object> selectInjury(Map<String, Object> param) throws Exception {
    Map<String, Object> rtnMap = new HashMap<String, Object>();
    rtnMap = previewMapper.selectInjury(param);

    if(rtnMap != null){
      String json = (String) rtnMap.get("injuries_json");
      ObjectMapper mapper = new ObjectMapper();
      List<HashMap<String, Object>> injuryList = mapper.readValue(json, new TypeReference<List<HashMap<String, Object>>>() {});
      injuryList.forEach(injury ->{
        param.put("playerId", injury.get("playerId"));
        Map<String, Object> playerMap = previewMapper.selectPlayerInfo(param);
        injury.put("positionNm", playerMap.get("positionShortNm"));
        injury.put("playerKrNm", playerMap.get("playerKrNm"));
      });
      rtnMap.put("injuries_json", mapper.writeValueAsString(injuryList));
    }
    return rtnMap;
  }
  
```
