// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.7.0 <0.9.0;

contract OrganizeEvent{
    struct Event{
        address organizer;
        string name;
        uint date;
        uint price;
        uint ticketCount;
        uint ticketRemain;
    }

    mapping(uint=>Event) public events;
    mapping(address=>mapping(uint=>uint)) public tickets;

    uint public nextId;

    function createEvent(string memory name, uint date, uint price, uint ticketCount) public{
        require(date > block.timestamp, "You can organize event in future");
        require(ticketCount > 0, "Must have atleast one ticket");
        events[nextId] = Event(msg.sender, name, date, price, ticketCount, ticketCount);
        nextId++;
    }

    function buyTicket(uint id, uint quantity) public payable{
        require(events[id].date != 0, "Event doesnt exist");
        require(events[id].date > block.timestamp, "Event already occured");
        Event storage _event = events[id];
        require(msg.value == (_event.price * quantity), "Ether not enough");
        require(_event.ticketRemain >= quantity, "Not enough tickets");
        _event.ticketRemain -= quantity;
        tickets[msg.sender][id] += quantity;
    }

    function transferTicket(uint id, uint quantity, address to) public{
        require(events[id].date != 0, "Event doesnt exist");
        require(events[id].date > block.timestamp, "Event already occured");
        require(events[id].ticketRemain >= quantity, "Not enough tickets");
        tickets[msg.sender][id] -= quantity;
        tickets[to][id] += quantity;
    }

}