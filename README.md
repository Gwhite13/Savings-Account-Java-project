# Savings-Account-Java-project
    import java.io.BufferedReader;
import java.io.FileReader;
import java.util.ArrayList;
import java.io.IOException;  
/**
 * Write a description of class mazee here.
 *
 * @author (your name)
 * @version (a version number or a date)
 */
public class mazee
{


  //Establishes entrance and exit locations on first row.
  private final int ENTRANCE_COL_POS = 1;
  private final int EXIT_COL_POS = 3;

  //Defines a character array to act as the maze.
  //col_pos & row_pos are the positions of a "cursor"-like entity that holds
  //...an evaluation position.

  private char[][] charMaze;
  private int col_pos;
  private int row_pos;
  public int COL_MAX;
  public int ROW_MAX;

  public mazee()
  {
    //Default maze constructor. 
    //Ideally, this constructor should not be called, but...who knows.

    col_pos = -1;
    row_pos = -1;
    COL_MAX = -1;
    ROW_MAX = -1;
    charMaze = new char[1][1];
    System.out.println("The default constructor maze was created.");
  }

  public mazee(String fileName) throws IOException
  {
    //Constructor that creates a maze by reading maze data from a file.
  
    BufferedReader buffReader = new BufferedReader(new FileReader(fileName));
    String current_line = buffReader.readLine(); //Gets the first line to size the maze.

    int spaceNdx; //Find the space separating row and column.

    spaceNdx = current_line.indexOf(" ");

    //Uses the space index to set the max rows and cols of the maze.
    ROW_MAX = Integer.parseInt(current_line.substring(0, spaceNdx));
    COL_MAX = Integer.parseInt(current_line.substring(spaceNdx + 1, current_line.length()));
    col_pos = -1;
    row_pos = -1;

    //Reads each line of the file after the sizes and uses addMazeRow to build each
    //...row of the maze.    
    charMaze = new char[ROW_MAX][COL_MAX];
    while ((current_line = buffReader.readLine()) != null)
    {
      row_pos++;
      addMazeRow(current_line);
    }

    //Closing buffered reader as the class now has all the information
    //...it needs to solve the maze.
    buffReader.close();

    ROW_MAX -= 1;
    COL_MAX -= 1;

  }

  private void addMazeRow(String line)
  {
    //Adds a new row to the maze by converting the given line into a character array
    //...and tacking the contents onto charMaze;

    char[] charArray = line.toCharArray();
    for (char charVal:charArray)
    {
      if (row_pos < ROW_MAX && col_pos < COL_MAX)
      {
        
        col_pos++;
        charMaze[row_pos][col_pos] = charVal;
        
      }
      else
      {
        System.out.println("Trouble in addMazeRow:\nA character entry is exceeding maze dimesions.");
      }
    }
    col_pos = -1;
  }

  public void resetCursor()
  {
    row_pos = 0;
    col_pos = ENTRANCE_COL_POS;
  }


  @Override
  public String toString()
  {
    String path = this.solver(0,1);
    if (path.charAt(path.length() - 1) == '!')
    {
      path = path.substring(0, path.length()-1);
      path = "Solved! Direct path from entrance to exit: " + path;
    }
    else
    {
      path = "Unsolvable!";
    }

    for(int i = 0; i <= ROW_MAX; i++)
    {
      String str = new String(charMaze[i]);
      path = path + "\n" + str;
    }
    return path;
  }
public String solver(int r_pos, int c_pos)
{
   // Variables for the Code
   ArrayList<String> dirList=new ArrayList<String>();
   String path = "";

   // Checks to see if at exit
   if (r_pos == 0 && c_pos == 3)
   {
      charMaze[r_pos][c_pos] = '!';
      return "!";
   }
   else
   {
      // Finds all possible directions
      if (c_pos < COL_MAX && charMaze[r_pos][c_pos + 1] == ' ')
      {
         dirList.add("East");
      }
      
      if (r_pos > 0 && charMaze[r_pos - 1][c_pos] == ' ')
      {
         dirList.add("North");
      }
      
      if (r_pos < ROW_MAX && charMaze[r_pos + 1][c_pos] == ' ')
      {
         dirList.add("South");
      }
      
      if (c_pos > 0 && charMaze[r_pos][c_pos - 1] == ' ')
      {
         dirList.add("West");
      }
      
      if (dirList.isEmpty())
      {
         charMaze[r_pos][c_pos] = 'X';
         return "X";
      }



      // Checks all reasonable directions for a solution
      for(String dir : dirList)
      {
         switch (dir)
         {
            case "East":
               charMaze[r_pos][c_pos] = 'E';
               path = "E" + solver(r_pos, c_pos + 1);
               if(path.charAt(path.length() - 1) == '!')
               {
                  return path;
               }
               break;

            case "North":
               charMaze[r_pos][c_pos] = 'N';
               path = "N" + solver(r_pos - 1, c_pos);
               if(path.charAt(path.length() - 1) == '!')
               {
                  return path;
               }
               break;

            case "South":
               charMaze[r_pos][c_pos] = 'S';
               path = "S" + solver(r_pos + 1, c_pos);
               if(path.charAt(path.length() - 1) == '!')
               {
                  return path;
               }
               break;

            case "West":
               charMaze[r_pos][c_pos] = 'W';
               path = "W" + solver(r_pos, c_pos - 1);
               if(path.charAt(path.length() - 1) == '!')
               {
                  return path;
               }
               break;

            default:
               //No Man's Land
         }
      }

      return path;
   }
   
}


}
