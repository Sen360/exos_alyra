Solidity

Execice 1:

pragma solidity 0.8.9;

contract Whitelist {
  mapping(address => bollean) whitelist;
}


Exercice 2:

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
}


Exercice 3: 

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
  
  function addPerson(string _name, uint _age) public {
    Person memomory person = Person(_name, _age);
  }
}

Exercice 4:

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
  
  Person[] public people;
}

Exercice 5:

pragma solidity 0.8.9;

contract Whitelist {
  struct Person {
    string name;
    uint age;
  }
  
  Person[] public people;
  
  function add(string _name, uint _age) public {
    Person memory person = Person(_name, _uint);
    people.push(person);
  } 

  function remove() public {
      people.pop();
  }
}

Exercice 6: 

pragma solidity 0.8.9;

contract Whitelist {
  mapping(address => bollean) whitelist;
  event Authorized(address _address);
}

