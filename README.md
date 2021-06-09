# VHDL-CarryLookAHead

#Top Module

    library IEEE;
    use IEEE.STD_LOGIC_1164.ALL;
    -----------------------------------------------
    entity carry_look_ahead is
    Port ( in_1 : in  STD_LOGIC_VECTOR (3 downto 0) := "1100";
           in_2 : in  STD_LOGIC_VECTOR (3 downto 0) := "1001";
           sum : out  STD_LOGIC_VECTOR (3 downto 0);
           cout : out  STD_LOGIC);
    end carry_look_ahead;
    -----------------------------------------------
    architecture Behavioral of carry_look_ahead is

    COMPONENT FA is
    Port ( a : in  STD_LOGIC;
           b : in  STD_LOGIC;
           cin : in  STD_LOGIC;
           s : out  STD_LOGIC;
           c : out  STD_LOGIC);
    end COMPONENT FA;


    COMPONENT carry_look_ahead_logic is
    Port ( a : in  STD_LOGIC_VECTOR (3 downto 0);
           b : in  STD_LOGIC_VECTOR (3 downto 0);
           cin : in  STD_LOGIC;
           c : out  STD_LOGIC_VECTOR (4 downto 0));
    end COMPONENT carry_look_ahead_logic;

    SIGNAL c : STD_LOGIC_VECTOR (4 DOWNTO 0);


    begin

    carry_look_ahead_logic_1 : carry_look_ahead_logic PORT MAP (in_1, in_2, '0', c); 
    FA_1 : FA port map(in_1(0), in_2(0), '0', sum(0), open);
    FA_2 : FA port map(in_1(1), in_2(1), c(1), sum(1), open);
    FA_3 : FA port map(in_1(2), in_2(2), c(2), sum(2), open);
    FA_4 : FA port map(in_1(3), in_2(3), c(3), sum(3), open);

    cout <= c(4);
    end Behavioral;


#Sub Madule:Full Adder

    library IEEE;
    use IEEE.STD_LOGIC_1164.ALL;
    --------------------------------------------------
    entity FA is
      Port ( a : in  STD_LOGIC;
           b : in  STD_LOGIC;
           cin : in  STD_LOGIC;
           s : out  STD_LOGIC;
           c : out  STD_LOGIC);
    end FA;

    architecture Behavioral of FA is

    begin
    s <= a xor b xor cin;
    c <= (a and b) or (a and cin) or (b and cin);

    end Behavioral;


#Sub Madule : carry_look_ahead_logic

    library IEEE;
    use IEEE.STD_LOGIC_1164.ALL;
    ---------------------------------------------------
    entity carry_look_ahead_logic is
    Port ( a : in  STD_LOGIC_VECTOR (3 downto 0);
           b : in  STD_LOGIC_VECTOR (3 downto 0);
           cin : in  STD_LOGIC;
           c : out  STD_LOGIC_VECTOR (4 downto 0));
    end carry_look_ahead_logic;
    ---------------------------------------------------
    architecture Behavioral of carry_look_ahead_logic is

    SIGNAL p, g : STD_LOGIC_VECTOR (3 downto 0);
    SIGNAL carry : STD_LOGIC_VECTOR (4 downto 0);

    begin

    g(0) <= a(0) and b(0);
    g(1) <= a(1) and b(1);
    g(2) <= a(2) and b(2);
    g(3) <= a(3) and b(3);

    p(0) <= a(0) xor b(0);
    p(1) <= a(1) xor b(1);
    p(2) <= a(2) xor b(2);
    p(3) <= a(3) xor b(3);

    carry(0) <= cin;
    carry(1) <= g(0) or (p(0) and carry(0));
    carry(2) <= g(1) or (p(1) and carry(1));
    carry(3) <= g(2) or (p(2) and carry(2));
    carry(4) <= g(3) or (p(3) and carry(3));
  
    c <= carry;

    end Behavioral;
    
#TestBench

    LIBRARY ieee;
    USE ieee.std_logic_1164.ALL;
 
    -- Uncomment the following library declaration if using
    -- arithmetic functions with Signed or Unsigned values
    --USE ieee.numeric_std.ALL;
 
    ENTITY carry_look_ahead_tb IS
    END carry_look_ahead_tb;
 
    ARCHITECTURE behavior OF carry_look_ahead_tb IS 
 
    -- Component Declaration for the Unit Under Test (UUT)
 
    COMPONENT carry_look_ahead
    PORT(
         in_1 : IN  std_logic_vector(3 downto 0);
         in_2 : IN  std_logic_vector(3 downto 0);
         sum : OUT  std_logic_vector(3 downto 0);
         cout : OUT  std_logic
        );
    END COMPONENT;
    

       --Inputs
       signal in_1 : std_logic_vector(3 downto 0) := (others => '0');
       signal in_2 : std_logic_vector(3 downto 0) := (others => '0');

 	--Outputs
       signal sum : std_logic_vector(3 downto 0);
      signal cout : std_logic;
       -- No clocks detected in port list. Replace <clock> below with 
       -- appropriate port name 
 

 
    BEGIN
 
	-- Instantiate the Unit Under Test (UUT)
      uut: carry_look_ahead PORT MAP (
          in_1 => in_1,
          in_2 => in_2,
          sum => sum,
          cout => cout
        );

    -- Clock process definitions
   
 

    -- Stimulus process
    stim_proc: process
     begin		
      -- hold reset state for 100 ns.
      wait for 10 ns;	
		in_1 <= "1100";
		in_2 <= "1001";
		
		wait for 10 ns;	
		in_1 <= "1101";
		in_2 <= "1001";
		
		wait for 10 ns;	
		in_1 <= "1110";
		in_2 <= "1001";
		
		wait for 10 ns;	
		in_1 <= "1111";
		in_2 <= "1001";
		wait for 10 ns;
      

      -- insert stimulus here 

      wait;
    end process;

    END;



