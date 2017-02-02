# xml parsing :: javax.xml.bind.*
<hr/>

```
  /**
   * Unmarshaller 생성 - no thread safe이기 때문에 항시 생성해서 처리하도록 한다. 
   * @method unmarshal
   * @see
   * @return Unmarshaller
   */
  private Unmarshaller getUnmarshaller() {
    JAXBContext jaxbContext = null;
    Unmarshaller jaxbUnmarshaller = null;
    try {
      jaxbContext = JAXBContext.newInstance(Statistics.class);
      jaxbUnmarshaller = jaxbContext.createUnmarshaller();
    } catch (UnmarshalException unex) {
      log.error(unex);
    } catch (JAXBException jaex) {
      log.error(jaex);
    }     
    return jaxbUnmarshaller;
  }
  
    /**
   * xml feed 받기 
   * @method getXmlFeed
   * @see
   * @param strUrl
   * @return
   * @throws Exception String
   */
  private String getXmlFeed(String strUrl) throws Exception 
  {
    Writer writer = new StringWriter();
    char[] buffer = new char[1024];
    InputStream is = null;
    URL url = null;
    HttpURLConnection httpUrl = null;
    try {
      log.info(strUrl);
      
      url = new URL(strUrl);
      httpUrl = (HttpURLConnection) url.openConnection();
      httpUrl.setRequestProperty("Authorization", "Basic " + AUTH);
      httpUrl.setReadTimeout( STATS_CONN_TIMEOUT );
      
      is = httpUrl.getInputStream();
      Reader reader = new BufferedReader(new InputStreamReader(is,"utf-8"));
      int n;
      while ((n = reader.read(buffer)) != -1) {
          writer.write(buffer, 0, n);
      }
    } catch(Exception ex) {
      log.error(ex.getMessage());
      throw ex;
    } finally {
      if(is!=null) is.close();
      if(httpUrl!=null) httpUrl.disconnect();
    }
    
    //log.info(writer.toString());
    return writer.toString();
  }  
  
 // call  
 this.getUnmarshaller().unmarshal(new StringReader(this.getXmlFeed("http://.."))); 
 
 
 ```
 
 <hr/>
 
 
  
  
