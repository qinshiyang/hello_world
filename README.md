# hello_world
I love my family,I love peace world,I love you.
preview changes
from transitions import Machine
import random

class StateMachine(object):

    states = ['IDLE','READY_TO_LOAD','TESTING','DONE','OK_TO_UNLOAD']

    def __init__(self):
        self.fixture = False  # initial fixture state
        self.DUT_engaged = False

        # initial Machine
        self.machine = Machine(model=self, states=StateMachine.states, initial='IDLE')

        self.machine.add_transition(trigger='dut_ready', source='IDLE', dest='READY_TO_LOAD',conditions=['fixture_open'])
        self.machine.add_transition(trigger='dut_ready', source='IDLE', dest='IDLE')

        self.machine.add_transition(trigger='start', source='READY_TO_LOAD', dest='TESTING',before='start_all_sequences',conditions=['engage_dut'])

        self.machine.add_transition(trigger='finish', source='TESTING', dest='DONE',conditions=['all_sites_are_finished'])
        self.machine.add_transition(trigger='UOP', source='TESTING', dest='IDLE',conditions=['all_sites_are_finished'])

        self.machine.add_transition(trigger='will_unload', source='DONE', dest='OK_TO_UNLOAD',before='fixture_disengag')

        self.machine.add_transition(trigger='dut_removed', source='OK_TO_UNLOAD', dest='IDLE',before='fixture_close')

    def fixture_open(self):
        self.fixture = random.random() < 0.5   #true or false
        return self.fixture

    def fixture_close(self):
        self.fixture = False
        return self.fixture

    def engage_dut(self):
        self.DUT_engaged =True
        return self.DUT_engaged

    def start_all_sequences(self):
        print("start_all_sequences")

    def all_sites_are_finished(self):
        sites_are_finished = True
        return sites_are_finished

    def fixture_disengag(self):
        print("fixture_disengag")


# if __name__ == '__main__':
#
#     statemachine = StateMachine()
#     print(statemachine.state)
#
#     statemachine.dut_ready()
#     print(statemachine.state)
#
#     if statemachine.state == 'READY_TO_LOAD':
#
#         statemachine.start()
#         print(statemachine.state)
#
#         # statemachine.UOP()
#         # print(statemachine.state)
#
#         statemachine.finish()
#         print(statemachine.state)
#
#         statemachine.will_unload()
#         print(statemachine.state)
#
#         statemachine.dut_removed()
#         print(statemachine.state)
#         print(statemachine.fixture)  # Indicates that the fixture_close function is called

