JSON-tree-structure-for-CQ5-node
================================

Generate JSON tree structure from cq5 content node. Given CQ5 content path, the code will generate JSON tree struture to any depth
        
        
        
        
        int counterChildren = 0;
        boolean topLeafBuilder = true;
        public String getJsonTreeBuilder(StringBuilder builders, Node node) throws RepositoryException, IOException{ 

                if(topLeafBuilder)
                 builders.append("[");//start of json array, to be added once
                 if( node.getProperty("jcr:primaryType").getString().equals("cq:Page") && node.hasNodes()){
                        int counterChildren = 0;
                        builders.append("{"+'"'+"text"+'"'+":"+'"'+node.getName()+'"'+","+'"'+"leaf"+'"'+":"+false+","+'"'+"cls"+'"'+":"+'"'+"folder"+'"'+","+'"'+"children"+'"'+":[");
                        NodeIterator pages =  node.hasNodes() ? node.getNodes() : null; 
       
                        if(pages != null){  
                        
                           while(pages.hasNext()){                  
                                Node page = pages.nextNode();
                                 
                                 NodeIterator pageFilter =  page.hasNodes() ? page.getNodes() : null;               
                                if(pageFilter != null && (pageFilter.getSize() > 1 || pageFilter.nextNode().getProperty("jcr:primaryType").getString().equals("cq:Page"))){
  
                                       log.info("pageFilter name is :"+page.getName());
                                       topLeafBuilder = false;
                                       getJsonTreeBuilder(page); // recursive call to n depth of node
                                       if(pages.hasNext() && (counterChildren != 0 && page.getProperty("jcr:primaryType").getString().equals("cq:Page"))) {//end of json object, append comma
                                          builders.append(",");     
     
                                     }
                                }
                                else if(!page.getName().equals("jcr:content") ){  // ignore jcr:content     
                                  
                                        builders.append("{"+'"'+"text"+'"'+":"+'"'+page.getName()+'"'+","+'"'+"leaf"+'"'+":"+true+","+'"'+"cls"+'"'+":"+'"'+"file"+'"'+"}");
                                        if(pages.hasNext() )
                                            builders.append(","); 
                                 }
                                 counterChildren++;
                              }
                              if(builders.substring(builders.length() - 1).equals(",")){//sanitise "," ending trace
                                  builders.deleteCharAt(builders.lastIndexOf(","));
                              }
                              builders.append("]}");                
                           
                        }
 
              }

            return builders.toString()+"]";                             
        
        }
