/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */
package gridprinter;

//Example print result
//00, 01, 02, 03
//11, 12, 13, 04
//10, 15, 14, 05
//09, 08, 07, 06

/**
 * This program takes an int[] and prints a table wrapping the ints in the array around a table. Starts at the top left and wraps around clockwise.
 * @author Napu Taitano
 */
public class GridPrinter {
    
    //Sample data
    static int[] data = {0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31,32,33,34,35,36,37,38,39,40,41,42,43,44,45,46,47,48,49,50,51,52,53,54,55};
    //static int[] data = {0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16};
    
    static int[][] table;

    /**
     * @param args the command line arguments
     */
    public static void main(String[] args) {        
        
        //Get dimensions needed to hold all of the numbers
        int[] dimensions = getRequiredTableDimensions(data);
        
        //Create the 2d array
        //Note for creating the 2d array, the first bracket is height, and the second width
        table = new int[dimensions[1]][dimensions[0]];
        
        //Fill the table
        fillTableClockwise(table, data);
        
        //Print the table
        printTable(table);
    }
    
    /**
     * Function calculates and returns the needed table dimensions to hold the items in the passed array
     * @param passedArray the array you would like to get dimensions for
     * @return the required dimensions
     */
    private static int[] getRequiredTableDimensions(int[] passedArray){
        //Default to 0 0
        int[] returnSize = {0,0};
        //While it's not big enough
        while((returnSize[0]*returnSize[1]) < passedArray.length){
            //If it's wider than high
            if(returnSize[0] > returnSize[1]){
                //Increate height
                returnSize[1]++;
            //else
            }else{
                //Increase width
                returnSize[0]++;
            }
        }
        //When done, return the needed dimensions
        return returnSize;
    }
    
    /**
     * Function fills the table clockwise
     * @param passedTable The table to be filled
     * @param passedData The data to fill the table with
     */
    private static void fillTableClockwise(int[][] passedTable, int[] passedData){
        //Get the width of the table
        int width = passedTable[0].length;
        //Get teh height of the table
        int height = passedTable.length;
        //For each row
        for(int i = 0; i < height; i++){
            
            //Get distance from the y edges
            int distanceFromYEdge = getDistanceFromEdgeOfArray(passedTable.length, i);
            
            //For each column
            for(int j = 0; j < width; j++){
                
                //Get distance from the x edges
                int distanceFromXEdge = getDistanceFromEdgeOfArray(passedTable[0].length, j);
                
                //Depth shows how deep into the rectangle of numbers the current cell is
                //Default depth to 0
                int depth = 0;
                //Set the depth to whichever distance is least
                if(distanceFromYEdge < distanceFromXEdge) {
                    depth = distanceFromYEdge;
                }else{
                    depth = distanceFromXEdge;
                }
                
                //Set the min value of the current cell based on depth
                int minValue = 0;
                //For each layer of depth
                for(int k = 0; k < depth; k++){
                    //Add to the min value to the total amount of numbers that make up the ring of it's parent
                    minValue += ((width - (k*2)) * 2) + ((height - (k*2)) * 2) - 4;
                }
                
                //If statement checks if it's on the bottom left, or the top right of the current ring
                //If it's bottom left
                if(height - (depth*2) > 1 && (i==height-1 - depth && j != width-1 - depth) || j==0 + depth && i != 0 + depth){
                    //If the cell would be out of bounds of the provided data
                    if(width - (depth*2) + height - (depth*2) - 2 + minValue+(width-1-j)+(height-1-i)-(depth*2) > passedData.length-1){
                        //Set it to 0
                        passedTable[i][j] = 0;
                    //Otherwise
                    }else{
                        //Set it (below)
                        //The index value is calculated with a complicated scramble of math I worked out that I'm not sure I understand anymore, but I'll try anyway.
                        
                        //(width-1 + height-1) calculates where the upper right half would have stopped counting, (depth) and (minValue) takes care of layers.
                        //(width-1-j) and (height-1-i) work out counting down clockwise
                        //I think. This was hard to decipher, and I'm the one who made it in the first place :P
                        passedTable[i][j] = passedData[(width - 1 - (depth*2)) + (height - 1 - (depth*2)) - (depth*2) + minValue +(width-1-j)+(height-1-i)];
                    }
                //Otherwise
                }else{
                    //If the cell would be out of bounds of the provided data
                    if(minValue+j+i-(depth*2) > passedData.length-1){
                        //Set it to 0
                        passedTable[i][j] = 0;
                    //Otherwise
                    }else{
                        //Set it
                        //j+i counts up from the top left to the bottom right around the edge. Add in (minValue) and (depth*2) for depth, and you're good.
                        //WAY simpler than the bit above.
                        passedTable[i][j] = passedData[j+i + minValue-(depth*2)];
                    }
                }
            }
        }
    }
    
    private static void printTable(int[][] passedTable){
        //For each row
        for(int i = 0; i < passedTable.length; i++){
            //For each column
            for(int j = 0; j < passedTable[0].length; j++){
                //Get the value
                int value = passedTable[i][j];
                //Convert to string
                String printValue = String.valueOf(value);
                //Add a 0 if needed
                if(printValue.length() == 1) printValue = "0"+printValue;
                //If at the last column of the grid
                if(passedTable[0].length-1 == j){
                    //use println
                    System.out.println(printValue+",");
                //else
                }else{
                    //use print
                    System.out.print(printValue+",");
                }
            }
        }
    }
    
    private static int getDistanceFromEdgeOfArray(int arrayLength, int cell){
        //Prepare the variable to return
        int distanceFromEdge = 0;
        //If you're closer to the end
        if(arrayLength - cell - 1 < cell){
            //Set the distance
            distanceFromEdge = arrayLength - cell - 1;
        //else
        }else{
            //Set the distance
            distanceFromEdge = cell;
        }
        //Return result
        return distanceFromEdge;
    }
    
}
