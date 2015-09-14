title: 行为型设计模式 — 责任链模式
date: 2014-08-30 19:00:57.0
tags:
- 设计模式
- 责任链模式
categories:
- 设计模式

---

什么是责任链模式责任链模式下，有多个对象，每个对象持有下一个对象的引用，从而形成一条链，客户端发出的请求在这条链上进行传递，直到最后一个对象处理完这个请求。责任链模式的类图如下：责任链模式下有两种角色：Hander：抽象处理者角色，提供设置下一个处理者的setter方法，并提供接受处理请求的接口ConcreteHandler：具体处理者角色，实现处理请求的方法，若没有下一个处理者，则处理请求示例程...

<!-- more -->

#### **什么是责任链模式** ####

责任链模式下，有多个对象，每个对象持有下一个对象的引用，从而形成一条链，客户端发出的请求在这条链上进行传递，直到最后一个对象处理完这个请求。

责任链模式的类图如下：

![Image 1][]  


  


责任链模式下有两种角色：

**Hander**：抽象处理者角色，提供设置下一个处理者的setter方法，并提供接受处理请求的接口

**ConcreteHandler**：具体处理者角色，实现处理请求的方法，若没有下一个处理者，则处理请求

  


示例程序：

Handler抽象类

    package patterns.behavior.respchain;
    
    /**
     * Abstract handler
     *
     * @author Charles Chen
     * @date 2014年8月30日
     */
    public abstract class Handler {
         protected Handler handler;
    
         public Handler getHandler() {
                return handler;
         }
    
         public void setHandler(Handler handler) {
                this. handler = handler;
         }
    
         public abstract void handleRequest();
    }

  


ConcreteHandler具体处理者类

    package patterns.behavior.respchain;
    
    /**
     * Concrete handler
     *
     * @author Charles Chen
     * @date 2014年8月30日
     */
    public class ConcreteHandler extends Handler {
    
         @Override
         public void handleRequest() {
                // TODO Auto-generated method stub
                if ( this.getHandler() != null) {
                    System. out.println( "Pass request to next handler");
                     this.getHandler().handleRequest();
                     return;
               }
    
               System. out.println( "No next handler, process the request");
         }
    
    }

  


  


客户端测试程序

    package patterns.behavior.respchain;
    
    /**
     * Client test for responsibility chain design pattern
     *
     * @author Charles Chen
     * @date 2014年8月30日
     */
    public class ClientTest {
    
         public static void main(String[] args) {
                // TODO Auto-generated method stub
               Handler handler1 = new ConcreteHandler();
               Handler handler2 = new ConcreteHandler();
                // Set handler2 as the next handler of handler1
                handler1.setHandler( handler2);
                // Request the handler1
                handler1.handleRequest();
         }
    
    }

  


####     ####

#### **为什么要使用责任链模式** ####

（1）责任链模式使得客户端请求与具体的处理逻辑解耦，我们可以在不影响客户端请求的前提下，自由调整和组织请求的处理逻辑

（2）责任链模式有利于处理流程的扩展，当需求引入新的处理者，只需要实现新的处理者，调整局部处理链即可，符合“开闭原则”

  


#### **责任链模式实现过滤器** ####

Tomcat服务器为web应用提供了Filter过滤器机制，struts也提供了类似的过滤器功能。这种过滤器机制就是基于责任链模式实现的。

过滤器的设计思想是，通过配置信息将过滤器实例添加到一个过滤器链中，相当于在过滤器链中维护各个过滤器的处理次序，客户端请求会依次在过滤器链上传递，直至最后一个过滤器处理完毕，才将请求传递给服务器程序。

这里我们可以模拟这种过滤器实现机制，实现一个过滤器链**FilterChain**和过滤器接口**Filter**。

FilterChain持有Filter的集合，维护一个index变量表示当前等待处理请求的过滤器的下标，并提供addFilter和removeFilter方法实现体检和移除过滤器。

FilterChain的doFilter方法用来接受请求，若当前index已经到达Filter集合最后一个元素，则表示所有过滤器都执行完毕，可以将请求传递给servlet，否则，则调用当前

index位置的Filter的doFilter方法，并将自身作为参数传入该方法

Filter提供doFilter方法处理请求，处理完毕后回调FilterChain的doFilter方法，实现将请求传递给下一个Filter实例

那么，当请求到来时，我们可以通过FilterChain的addFilter接口添加具体的Filter实例，并调用doFilter方法开始处理请求

这里我们实现了两个Filter：ScriptFilter（用来过滤请求内容中的script脚本）和SensitiveWordsFilter（用来过滤请求内容中的敏感词）

  


类图如下：

![Image 1][]  


  


过滤器执行序列图：

![Image 1][]  


  


示例程序：

Filter过滤器接口

    package patterns.behavior.respchain.filter;
    
    /**
     * Filter interface
     *
     * @author Charles Chen
     * @date 2014年8月30日
     */
    public interface Filter {
         void doFilter(Request request, FilterChain chain);
    }

  


FilterChain过滤器链

    package patterns.behavior.respchain.filter;
    
    import java.util.ArrayList;
    import java.util.List;
    
    public class FilterChain {
         private List<Filter> filters = new ArrayList<Filter>();
         private int index = 0;
    
         /**
          * Add a filter to filter list
          *
          * @param filter
          */
         public FilterChain addFilter(Filter filter) {
                this. filters.add( filter);
                return this;
         }
    
         /**
          * Remove a filter from filter list
          *
          * @param filter
          */
         public FilterChain removeFilter(Filter filter) {
                this. filters.remove( filter);
                return this;
         }
    
         /**
          * Call current filter to handle the request
          *
          * The current filter will callback this doFilter method to call next filter
          *
          * @param request
          */
         public void doFilter(Request request) {
                if ( index >= this. filters.size()) {
                    System. out
                               .println( "All filters are done, the request content after filters: "
                                         + request);
                     return;
               }
               Filter filter = this. filters.get( index++);
                filter.doFilter( request, this );
         }
    }

  


ScriptFilter脚本过滤器类

    package patterns.behavior.respchain.filter;
    
    /**
     * Filter the script in the requeset content
     *
     * @author Charles Chen
     * @date 2014年8月30日
     */
    public class ScriptFilter implements Filter {
    
         @Override
         public void doFilter(Request request, FilterChain chain) {
                // TODO Auto-generated method stub
               String content = request.getContent();
                content = content.replaceAll("<script[^>]*>.*</script>" , "" );
                request.setContent( content);
                chain.doFilter( request);
         }
    }

  


SensitiveWordsFilter敏感词过滤器类

    package patterns.behavior.respchain.filter;
    
    /**
     * Sensitive words filter
     *
     * @author Charles Chen
     * @date 2014年8月30日
     */
    public class SensitiveWordsFilter implements Filter {
         private String[] words;
    
         public SensitiveWordsFilter() {
                // TODO Auto-generated constructor stub
                words = new String[] { "fuck ", "shit", "hell" };
         }
    
         @Override
         public void doFilter(Request request, FilterChain chain) {
                // TODO Auto-generated method stub
               String content = request.getContent();
                for (String word : words) {
                     content = content.replaceAll( "(?i)" + word, "");
               }
                request.setContent( content);
                chain.doFilter( request);
         }
    }

  


客户端测试程序

    package patterns.behavior.respchain.filter;
    
    /**
     * Client test for filters using responsibility design pattern
     *
     * @author Charles Chen
     * @date 2014年8月30日
     */
    public class ClientTest {
    
         public static void main(String[] args) {
                // TODO Auto-generated method stub
               FilterChain chain = new FilterChain();
                chain.addFilter( new ScriptFilter()).addFilter( new SensitiveWordsFilter());
               Request request = new Request(
                          "This is a request<script>alert('script')</script>. Fuck" );
                chain.doFilter( request);
         }
    
    }

  


#### **枚举类型实现责任链模式** ####

我们知道Enum枚举类型的每个枚举值可以有自己的常量相关方法，利用这个特性，我们可以定义一个枚举类型，将每个处理者声明为一个枚举值，并实现常量相关方法。当请求到来时，只需要遍历枚举类型的所有枚举值，直到找到某一个枚举值可以处理这个请求，从而实现责任链模式。

  


例如Request请求实例包括请求方法method和请求内容content，请求方法有GET, POST, PUT, DELETE。我们可以定义一个枚举类型RequestHandler，其中声明四个枚举值用来分别处理四种请求内容的请求实例。示例代码如下：

  


    package patterns.behavior.respchain;
    /**
     * Responsibility chain design pattern with Enum
     * @author Charles Chen
     * @date 2014年9月4日
     */
    public class EnumRespChain {
    	/**
    	 * Request handler, every enum value is a concrete handler
    	 * @author Charles Chen
    	 * @date 2014年9月4日
    	 */
    	static enum RequestHandler {
    		HANDLE_GET {
    			public boolean handle(Request request) {
    				if (request.getMethod() == Request.Method.GET) {
    					System.out.println("Request Method: " + Request.Method.GET
    							+ ", Request Content: " + request.getContent());
    					return true;
    				}
    				return false;
    			}
    		},
    		HANDLE_POST {
    			public boolean handle(Request request) {
    				if (request.getMethod() == Request.Method.POST) {
    					System.out.println("Request Method: " + Request.Method.POST
    							+ ", Request Content: " + request.getContent());
    					return true;
    				}
    				return false;
    			}
    		},
    		HANDLE_PUT {
    			public boolean handle(Request request) {
    				if (request.getMethod() == Request.Method.PUT) {
    					System.out.println("Request Method: " + Request.Method.PUT
    							+ ", Request Content: " + request.getContent());
    					return true;
    				}
    				return false;
    			}
    		},
    		HANDLE_DELETE {
    			public boolean handle(Request request) {
    				if (request.getMethod() == Request.Method.DELETE) {
    					System.out.println("Request Method: "
    							+ Request.Method.DELETE + ", Request Content: "
    							+ request.getContent());
    					return true;
    				}
    				return false;
    			}
    		};
    
    		public abstract boolean handle(Request request);
    	}
    	
    	/**
    	 * The request POJO
    	 * @author Charles Chen
    	 * @date 2014年9月4日
    	 */
    	static class Request {
    		enum Method {
    			GET, POST, PUT, DELETE
    		}
    
    		private String content;
    		private Method method;
    
    		public Request(Method method, String content) {
    			// TODO Auto-generated constructor stub
    			this.method = method;
    			this.content = content;
    		}
    
    		public String getContent() {
    			return content;
    		}
    
    		public void setContent(String content) {
    			this.content = content;
    		}
    
    		public Method getMethod() {
    			return method;
    		}
    
    		public void setMethod(Method method) {
    			this.method = method;
    		}
    
    	}
    	
    	/**
    	 * Client test
    	 * @param args
    	 */
    	public static void main(String[] args) {
    		// TODO Auto-generated method stub
    		Request request = new Request(Request.Method.POST, "This is a HTTP request in POST method");
    		for(RequestHandler handler: RequestHandler.values()){
    			if(handler.handle(request))break;
    		}
    	}
    
    }

  


  



[Image 1]: 