package com;
import java.util.*;

public class SessionLimitChecker {

    public static List<String> getLimitBreaches(int[] timestamps, String[] userIds, String[] actions, int m) {
       Map<String, Integer> sessionCount=new HashMap<>();
       Set<String> breachedUser=new HashSet<>();
       
       int n=timestamps.length;
       
       for(int i=0;i<n;i++) {
    	   String user=userIds[i];
    	   String action=actions[i];
    	   
    	   sessionCount.putIfAbsent(user, 0);
    	   
    	   if(action.equals("login")) {
    		   sessionCount.put(user, sessionCount.get(user)+1);
    		   
    		   	if(sessionCount.get(user)>m) {
    		   		breachedUser.add(user);
    		   	}
    	   }else if(action.equals("logout")) {
		   		if(sessionCount.get(user)>0) {
		   			sessionCount.put(user, sessionCount.get(user)-1);
		   		}
		   	}
    	   
       }
       
       List<String> result=new ArrayList<String>(breachedUser);
       Collections.sort(result);
       return result;
    }

    // Sample usage
    public static void main(String[] args) {
        int[] timestamps = {1, 2, 3, 4};
        String[] userIds = {"A", "A", "A", "A"};
        String[] actions = {"login", "login", "login", "logout"};
        int m = 2;

        List<String> breaches = getLimitBreaches(timestamps, userIds, actions, m);
        System.out.println(breaches); // Output: [A]
    }
}
