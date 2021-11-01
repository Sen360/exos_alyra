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

Exercice - type de visibilité de fonctions

pragma solidity 0.6.0;

contract HelloWorld {
  string myString = "Hello World";
  
  function hello() public view returns (string memory) {
    return myString;
  }
}

Exercice - Whitelist

pragma solidity 0.8.9;

contract Whitelist {
  mapping(address => bollean) whitelist;
  event Authorized(address _address);
  
  function authorize(address _address) public {
    whitelist[_address] = true;
    emit Authorized(_address);
  }
}

Exercice variable spéciale
pragma solidity 0.8.9;

contract Time {
  function getTime() public view returns(uint) {
    return block.timestamp;
  }
}
