# html parsing :: org.jsoup.Jsoup.*

``` source

  private static final String AUTH = new String(Base64.encodeBase64("id:password".getBytes()));

  /**
   * feed 변경이력 조회 
   * @method addBaseballStatsFeedList
   * @see
   * @param statsType
   * @param feedUrlType
   * @param feedUrl
   * @throws Exception void
   */
  @Override
  @Transactional(rollbackFor=Exception.class)
  public void addBaseballStatsFeedList(final String statsType, final String feedUrlType, final String feedUrl) throws Exception {
    log.info("addBaseballStatsFeedList params = "+statsType+", "+feedUrlType+", "+feedUrl);  
    
    Document doc = Jsoup
          .connect(feedUrl)
          .timeout(STATS_CONN_TIMEOUT)
          .header("Authorization", "Basic " + AUTH)
          .get();
    Element prevEle = doc.select("pre").first();
    
    Pattern pattern  =  Pattern.compile("\\d+/\\d+/\\d+\\s\\d+:\\d+\\s(AM|PM)");
    DateFormat dformat = new SimpleDateFormat("MM/dd/yyyy hh:mm a", Locale.UK);

    List<StatsBaseballFeed> feeds = new ArrayList<StatsBaseballFeed>();
    for(Node node:prevEle.childNodes()) {
       if(node instanceof Element) {
         Element aEle = ((Element)node);

         if((StatsFeedUrlTypeEnum.FINAL.getType().equals(feedUrlType) 
                       && StatsFeedUrlTypeEnum.FINAL.isPrefixs(aEle.text()))
            || (StatsFeedUrlTypeEnum.LIVE.getType().equals(feedUrlType) 
                        && StatsFeedUrlTypeEnum.LIVE.isPrefixs(aEle.text()))) {
           
           log.debug(String.format("%s - %s", ((Element)node).text(), ((Element)node).attr("abs:href")));
           
           StatsBaseballFeed feed = new StatsBaseballFeed();
           feed.setStatsType(statsType);
           feed.setFileName(aEle.text());
           feed.setFileLink(aEle.attr("abs:href"));
           feed.setFileLinkType(feedUrlType);
           feed.setStatus(StatsFeedEnum.FEED.getCode());
          
           if(aEle.previousSibling()!=null) {
             Matcher match = pattern.matcher(((TextNode)aEle.previousSibling()).text());
             if(match.find()) {
               feed.setFileUptDt(dformat.parse(match.group()));
               log.debug(String.format("%s", match.group()));
             }
           }

           //BOXSCORE(=LINEUP)인 경우, 한번만 처리
           if(StatsFeedUrlTypeEnum.LIVE.isPrefixs(aEle.text())) {
             feed.setFileUptDt(dformat.parse("1/1/2016 00:00 AM"));
           }
           
           feeds.add(feed);
         }
       }
    }
    
    if(!feeds.isEmpty()) {
      StatsBaseballFeed entity = new StatsBaseballFeed();
      entity.setFeeds(feeds);
      statsBaseballMapper.insertStatsBaseballFeedList(entity);
    }
  }
  
```
